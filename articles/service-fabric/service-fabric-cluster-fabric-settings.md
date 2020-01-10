---
title: Alterar as configurações de Cluster Service Fabric do Azure
description: Este artigo descreve as configurações de malha e as políticas de atualização de malha que você pode personalizar.
ms.topic: reference
ms.date: 08/30/2019
ms.openlocfilehash: ba98d4d30d14cb3a1981652fc0b86354923a8851
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75772130"
---
# <a name="customize-service-fabric-cluster-settings"></a>Personalizar configurações de Cluster Service Fabric
Este artigo descreve as várias configurações de malha para o Cluster Service Fabric que você pode personalizar. Para clusters hospedados no Azure, você pode personalizar as configurações por meio do [portal do Azure](https://portal.azure.com) ou usando um modelo de Azure Resource Manager. Para obter mais informações, consulte [atualizar a configuração de um cluster do Azure](service-fabric-cluster-config-upgrade-azure.md). Para clusters autônomos, você personaliza as configurações atualizando o arquivo *ClusterConfig. JSON* e realizando uma atualização de configuração no cluster. Para obter mais informações, consulte [atualizar a configuração de um cluster autônomo](service-fabric-cluster-config-upgrade-windows-server.md).

Há três políticas de atualização diferentes:

- **Dinâmico** – as alterações em uma configuração dinâmica não fazem com que nenhum processo seja reiniciado de Service Fabric processos ou de seus processos de host de serviço. 
- **Estático** – as alterações em uma configuração estática farão com que o Service Fabric nó seja reiniciado para consumir a alteração. Os serviços nos nós serão reiniciados.
- Não **permitido** – essas configurações não podem ser modificadas. A alteração dessas configurações requer que o cluster seja destruído e um novo cluster seja criado. 

Veja a seguir uma lista de configurações de malha que você pode personalizar, organizadas por seção.

## <a name="applicationgatewayhttp"></a>ApplicationGateway/Http

| **Parâmetro** | **Valores permitidos** | **Atualizar política** | **Orientação ou descrição resumida** |
| --- | --- | --- | --- |
|ApplicationCertificateValidationPolicy|Cadeia de caracteres, o padrão é "nenhum"|Estático| Isso não valida o certificado do servidor; a solicitação foi concluída com sucesso. Consulte config ServiceCertificateThumbprints para obter a lista separada por vírgulas de impressões digitais dos certificados remotos que o proxy reverso pode confiar. Consulte config ServiceCommonNameAndIssuer para obter o nome da entidade e a impressão digital do emissor dos certificados remotos que o proxy reverso pode confiar. Para saber mais, consulte [conexão segura de proxy reverso](service-fabric-reverseproxy-configure-secure-communication.md#secure-connection-establishment-between-the-reverse-proxy-and-services). |
|BodyChunkSize |Uint, o padrão é 16384 |Dinâmico| Fornece o tamanho de para a parte em bytes usados para ler o corpo. |
|CrlCheckingFlag|uint, o padrão é 0x40000000 |Dinâmico| Sinalizadores para validação de cadeia de certificados de serviço/aplicativo; por exemplo, CRL verificando 0x10000000 CERT_CHAIN_REVOCATION_CHECK_END_CERT 0x20000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN 0x40000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT 0x80000000 CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY configuração como 0 desabilita a verificação de CRL a lista completa de valores com suporte é documentada por dwFlags de CertGetCertificateChain: https://msdn.microsoft.com/library/windows/desktop/aa376078(v=vs.85).aspx  |
|DefaultHttpRequestTimeout |Tempo em segundos. o padrão é 120 |Dinâmico|Especifique TimeSpan em segundos.  Fornece o tempo limite de solicitação padrão para as solicitações HTTP sendo processadas no gateway de aplicativo http. |
|ForwardClientCertificate|bool, o padrão é FALSE|Dinâmico|Quando definido como false, o proxy reverso não solicitará o certificado do cliente. Quando definido como true, o proxy reverso solicitará o certificado do cliente durante o handshake de SSL e encaminhará a cadeia de caracteres de formato PEM codificado em base64 para o serviço em um cabeçalho chamado X-Client-Certificate. o serviço pode falhar a solicitação com o código de status apropriado Depois de inspecionar os dados do certificado. Se isso for verdadeiro e o cliente não apresentar um certificado, o proxy reverso encaminhará um cabeçalho vazio e permitirá que o serviço manipule o caso. O proxy reverso funcionará como uma camada transparente. Para saber mais, confira [Configurar a autenticação de certificado do cliente](service-fabric-reverseproxy-configure-secure-communication.md#setting-up-client-certificate-authentication-through-the-reverse-proxy). |
|GatewayAuthCredentialType |Cadeia de caracteres, o padrão é "nenhum" |Estático| Indica o tipo de credenciais de segurança a ser usado no ponto de extremidade do gateway de aplicativo http os valores válidos são None/X509. |
|GatewayX509CertificateFindType |Cadeia de caracteres, o padrão é "FindByThumbprint" |Dinâmico| Indica como pesquisar o certificado no repositório especificado pelo valor com suporte GatewayX509CertificateStoreName: FindByThumbprint; FindBySubjectName. |
|GatewayX509CertificateFindValue | Cadeia de caracteres, o padrão é "" |Dinâmico| Valor do filtro de pesquisa usado para localizar o certificado de gateway de aplicativo http. Esse certificado é configurado no ponto de extremidade HTTPS e também pode ser usado para verificar a identidade do aplicativo, se necessário, dos serviços. FindValue é pesquisado primeiro; e se isso não existir; FindValueSecondary é pesquisado. |
|GatewayX509CertificateFindValueSecondary | Cadeia de caracteres, o padrão é "" |Dinâmico|Valor do filtro de pesquisa usado para localizar o certificado de gateway de aplicativo http. Esse certificado é configurado no ponto de extremidade HTTPS e também pode ser usado para verificar a identidade do aplicativo, se necessário, dos serviços. FindValue é pesquisado primeiro; e se isso não existir; FindValueSecondary é pesquisado.|
|GatewayX509CertificateStoreName |Cadeia de caracteres, o padrão é "My" |Dinâmico| Nome do repositório de certificados X. 509 que contém o certificado para o gateway de aplicativo http. |
|HttpRequestConnectTimeout|TimeSpan, o padrão é Common:: TimeSpan:: FromSeconds (5)|Dinâmico|Especifique TimeSpan em segundos.  Fornece o tempo limite de conexão para as solicitações HTTP que estão sendo enviadas do gateway de aplicativo http.  |
|IgnoreCrlOfflineError|bool, o padrão é TRUE|Dinâmico|Se deve ignorar o erro de CRL offline para verificação de certificado de serviço/aplicativo. |
|IsEnabled |Bool, o padrão é false |Estático| Habilita/desabilita o HttpApplicationGateway. O HttpApplicationGateway está desabilitado por padrão e essa configuração precisa ser definida para habilitá-lo. |
|NumberOfParallelOperations | Uint, o padrão é 5000 |Estático|Número de leituras a serem postadas na fila do servidor http. Isso controla o número de solicitações simultâneas que podem ser atendidas pelo HttpGateway. |
|RemoveServiceResponseHeaders|Cadeia de caracteres, o padrão é "data; Servidor|Estático|Lista de cabeçalhos de resposta separados por vírgulas/separadas que serão removidos da resposta do serviço; antes de encaminhá-lo para o cliente. Se for definido como uma cadeia de caracteres vazia; Passe todos os cabeçalhos retornados pelo serviço no estado em que se encontram. , Não substituir a data e o servidor |
|ResolveServiceBackoffInterval |Tempo em segundos, o padrão é 5 |Dinâmico|Especifique TimeSpan em segundos.  Fornece o intervalo de retirada padrão antes de tentar novamente uma operação de serviço de resolução com falha. |
|SecureOnlyMode|bool, o padrão é FALSE|Dinâmico| SecureOnlyMode: true: o proxy reverso só será encaminhado para serviços que publicam pontos de extremidade seguros. false: o proxy reverso pode encaminhar solicitações para pontos de extremidade seguros/não seguros. Para saber mais, confira [lógica de seleção de ponto de extremidade de proxy reverso](service-fabric-reverseproxy-configure-secure-communication.md#endpoint-selection-logic-when-services-expose-secure-as-well-as-unsecured-endpoints).  |
|ServiceCertificateThumbprints|Cadeia de caracteres, o padrão é ""|Dinâmico|A lista separada por vírgulas de impressões digitais dos certificados remotos que o proxy reverso pode confiar. Para saber mais, consulte [conexão segura de proxy reverso](service-fabric-reverseproxy-configure-secure-communication.md#secure-connection-establishment-between-the-reverse-proxy-and-services). |

## <a name="applicationgatewayhttpservicecommonnameandissuer"></a>ApplicationGateway/Http/ServiceCommonNameAndIssuer

| **Parâmetro** | **Valores permitidos** | **Atualizar política** | **Orientação ou descrição resumida** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, o padrão é None|Dinâmico| Nome da entidade e impressão digital do emissor dos certificados remotos que o proxy reverso pode confiar. Para saber mais, consulte [conexão segura de proxy reverso](service-fabric-reverseproxy-configure-secure-communication.md#secure-connection-establishment-between-the-reverse-proxy-and-services). |

## <a name="backuprestoreservice"></a>BackupRestoreService

| **Parâmetro** | **Valores permitidos** | **Atualizar política** | **Orientação ou descrição resumida** |
| --- | --- | --- | --- |
|MinReplicaSetSize|int, o padrão é 0|Estático|O MinReplicaSetSize para BackupRestoreService |
|PlacementConstraints|Cadeia de caracteres, o padrão é ""|Estático|  O PlacementConstraints para o serviço BackupRestore |
|SecretEncryptionCertThumbprint|Cadeia de caracteres, o padrão é ""|Dinâmico|Impressão digital do certificado X509 de criptografia secreta |
|SecretEncryptionCertX509StoreName|Cadeia de caracteres, o padrão é "My"|   Dinâmico|    Isso indica o certificado a ser usado para criptografia e descriptografia do nome de credenciais do repositório de certificados X. 509 que é usado para criptografar credenciais de armazenamento de descriptografia usadas pelo serviço de restauração de backup |
|TargetReplicaSetSize|int, o padrão é 0|Estático| O TargetReplicaSetSize para BackupRestoreService |

## <a name="clustermanager"></a>ClusterManager

| **Parâmetro** | **Valores permitidos** | **Atualizar política** | **Orientação ou descrição resumida** |
| --- | --- | --- | --- |
|AllowCustomUpgradeSortPolicies | Bool, o padrão é false |Dinâmico|Se as políticas de classificação de atualização personalizada são permitidas ou não. Isso é usado para executar a atualização de duas fases que habilita esse recurso. Service Fabric 6,5 adiciona suporte para especificar a política de classificação para domínios de atualização durante atualizações de cluster ou de aplicativo. As políticas com suporte são Numeric, lexicográfica, ReverseNumeric e ReverseLexicographical. O padrão é numérico. Para poder usar esse recurso, a configuração do manifesto do cluster Clustermanager/AllowCustomUpgradeSortPolicies deve ser definida como true como uma segunda etapa de atualização de configuração depois que o código da it 6,5 tiver concluído a atualização. É importante que isso seja feito em duas fases, caso contrário, a atualização do código pode ficar confusa sobre a ordem de atualização durante a primeira atualização.|
|EnableDefaultServicesUpgrade | Bool, o padrão é false |Dinâmico|Habilite a atualização de serviços padrão durante a atualização do aplicativo. As descrições de serviço padrão seriam substituídas após a atualização. |
|FabricUpgradeHealthCheckInterval |Tempo em segundos, o padrão é 60 |Dinâmico|A frequência de verificação do status de integridade durante uma atualização de malha monitorada |
|FabricUpgradeStatusPollInterval |Tempo em segundos, o padrão é 60 |Dinâmico|A frequência de sondagem do status de atualização do fabric. Esse valor determina a taxa de atualização para qualquer chamada GetFabricUpgradeProgress |
|ImageBuilderTimeoutBuffer |Tempo em segundos, o padrão é 3 |Dinâmico|Especifique TimeSpan em segundos. A quantidade de tempo para permitir que erros de tempo limite específicos do construtor de imagem retornem ao cliente. Se esse buffer for muito pequeno; em seguida, o cliente atinge o tempo limite antes do servidor e Obtém um erro de tempo limite genérico. |
|InfrastructureTaskHealthCheckRetryTimeout | Tempo em segundos, o padrão é 60 |Dinâmico|Especifique TimeSpan em segundos. O tempo gasto para repetir as verificações de integridade com falha durante o pós-processamento de uma tarefa de infraestrutura. Observar uma verificação de integridade aprovada redefinirá esse temporizador. |
|InfrastructureTaskHealthCheckStableDuration | Tempo em segundos, o padrão é 0|Dinâmico| Especifique TimeSpan em segundos. A quantidade de tempo para observar verificações de integridade passadas consecutivas antes de o pós-processamento de uma tarefa de infraestrutura ser concluído com êxito. A observação de uma verificação de integridade com falha redefinirá esse temporizador. |
|InfrastructureTaskHealthCheckWaitDuration |Tempo em segundos, o padrão é 0|Dinâmico| Especifique TimeSpan em segundos. A quantidade de tempo de espera antes de iniciar verificações de integridade após o pós-processamento de uma tarefa de infraestrutura. |
|InfrastructureTaskProcessingInterval | Tempo em segundos, o padrão é 10 |Dinâmico|Especifique TimeSpan em segundos. O intervalo de processamento usado pela máquina de estado de processamento de tarefa de infraestrutura. |
|MaxCommunicationTimeout |Tempo em segundos, o padrão é 600 |Dinâmico|Especifique TimeSpan em segundos. O tempo limite máximo para comunicações internas entre Clustermanager e outros serviços do sistema (ou seja,; Serviço de Nomenclatura; Gerenciador de Failover e etc.). Esse tempo limite deve ser menor do que o MaxOperationTimeout global (pois pode haver várias comunicações entre os componentes do sistema para cada operação do cliente). |
|MaxDataMigrationTimeout |Tempo em segundos, o padrão é 600 |Dinâmico|Especifique TimeSpan em segundos. O tempo limite máximo para operações de recuperação de migração de dados depois que uma atualização de malha foi feita. |
|MaxOperationRetryDelay |Tempo em segundos, o padrão é 5|Dinâmico| Especifique TimeSpan em segundos. O atraso máximo para repetições internas quando são encontradas falhas. |
|MaxOperationTimeout |Tempo em segundos, o padrão é MaxValue |Dinâmico| Especifique TimeSpan em segundos. O tempo limite global máximo para operações de processamento interno no Clustermanager. |
|MaxTimeoutRetryBuffer | Tempo em segundos, o padrão é 600 |Dinâmico|Especifique TimeSpan em segundos. O tempo limite máximo de operação ao tentar novamente internamente devido a tempos limite é `<Original Time out> + <MaxTimeoutRetryBuffer>`. O tempo limite adicional é adicionado em incrementos de MinOperationTimeout. |
|MinOperationTimeout | Tempo em segundos, o padrão é 60 |Dinâmico|Especifique TimeSpan em segundos. O tempo limite global mínimo para operações de processamento interno no Clustermanager. |
|MinReplicaSetSize |Int, o padrão é 3 |Não permitido|O MinReplicaSetSize para Clustermanager. |
|PlacementConstraints | Cadeia de caracteres, o padrão é "" |Não permitido|O PlacementConstraints para Clustermanager. |
|QuorumLossWaitDuration |Tempo em segundos, o padrão é MaxValue |Não permitido| Especifique TimeSpan em segundos. O QuorumLossWaitDuration para Clustermanager. |
|ReplicaRestartWaitDuration |Tempo em segundos, o padrão é (60,0 \* 30)|Não permitido|Especifique TimeSpan em segundos. O ReplicaRestartWaitDuration para Clustermanager. |
|ReplicaSetCheckTimeoutRollbackOverride |Tempo em segundos, o padrão é 1200 |Dinâmico| Especifique TimeSpan em segundos. Se ReplicaSetCheckTimeout for definido como o valor máximo de DWORD; em seguida, ele é substituído pelo valor dessa configuração para fins de reversão. O valor usado para roll forward nunca é substituído. |
|SkipRollbackUpdateDefaultService | Bool, o padrão é false |Dinâmico|O CM ignorará a reversão dos serviços padrão atualizados durante a reversão da atualização do aplicativo. |
|StandByReplicaKeepDuration | Tempo em segundos, o padrão é (3600,0 \* 2)|Não permitido|Especifique TimeSpan em segundos. O StandByReplicaKeepDuration para Clustermanager. |
|TargetReplicaSetSize |Int, o padrão é 7 |Não permitido|O TargetReplicaSetSize para Clustermanager. |
|UpgradeHealthCheckInterval |Tempo em segundos, o padrão é 60 |Dinâmico|A frequência de verificações de status de integridade durante um aplicativo monitorado atualiza |
|UpgradeStatusPollInterval |Tempo em segundos, o padrão é 60 |Dinâmico|A frequência de sondagem do status de atualização do aplicativo. Esse valor determina a taxa de atualização para qualquer chamada GetApplicationUpgradeProgress |

## <a name="common"></a>Common

| **Parâmetro** | **Valores permitidos** | **Atualizar política** | **Orientação ou descrição resumida** |
| --- | --- | --- | --- |
|PerfMonitorInterval |Tempo em segundos, o padrão é 1 |Dinâmico|Especifique TimeSpan em segundos. Intervalo de monitoramento de desempenho. Definir como 0 ou valor negativo desabilita o monitoramento. |

## <a name="defragmentationemptynodedistributionpolicy"></a>DefragmentationEmptyNodeDistributionPolicy
| **Parâmetro** | **Valores permitidos** |**Atualizar política**| **Orientação ou descrição resumida** |
| --- | --- | --- | --- |
|PropertyGroup|KeyIntegerValueMap, o padrão é None|Dinâmico|Especifica a desfragmentação da política a seguir ao esvaziar nós. Para uma determinada métrica 0 indica que o it deve tentar desfragmentar os nós uniformemente entre UDs e FDs; 1 indica somente que os nós devem ser desfragmentados |

## <a name="defragmentationmetrics"></a>DefragmentationMetrics
| **Parâmetro** | **Valores permitidos** |**Atualizar política**| **Orientação ou descrição resumida** |
| --- | --- | --- | --- |
|PropertyGroup|KeyBoolValueMap, o padrão é None|Dinâmico|Determina o conjunto de métricas que devem ser usadas para desfragmentação e não para balanceamento de carga. |

## <a name="defragmentationmetricspercentornumberofemptynodestriggeringthreshold"></a>DefragmentationMetricsPercentOrNumberOfEmptyNodesTriggeringThreshold
| **Parâmetro** | **Valores permitidos** |**Atualizar política**| **Orientação ou descrição resumida** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, o padrão é None|Dinâmico|Determina o número de nós livres que são necessários para considerar o cluster desfragmentado, especificando o percentual no intervalo [0,0-1,0] ou o número de nós vazios como número > = 1,0 |

## <a name="diagnostics"></a>Diagnósticos

| **Parâmetro** | **Valores permitidos** | **Atualizar política** | **Orientação ou descrição resumida** |
| --- | --- | --- | --- |
|AdminOnlyHttpAudit |Bool, o padrão é true | Dinâmico | Exclua as solicitações HTTP que não afetam o estado do cluster da auditoria. Existentes somente as solicitações do tipo "GET" são excluídas; Mas isso está sujeito a alterações. |
|AppDiagnosticStoreAccessRequiresImpersonation |Bool, o padrão é true | Dinâmico |Se a representação é necessária ou não ao acessar armazenamentos de diagnóstico em nome do aplicativo. |
|AppEtwTraceDeletionAgeInDays |Int, o padrão é 3 | Dinâmico |Número de dias após o qual excluimos arquivos ETL antigos que contêm rastreamentos de ETW do aplicativo. |
|ApplicationLogsFormatVersion |Int, o padrão é 0 | Dinâmico |Versão do formato de logs de aplicativo. Os valores com suporte são 0 e 1. A versão 1 inclui mais campos do registro de evento do ETW que a versão 0. |
|AuditHttpRequests |Bool, o padrão é false | Dinâmico | Ativar ou desativar a auditoria HTTP. A finalidade da auditoria é ver as atividades que foram executadas no cluster; incluindo quem iniciou a solicitação. Observe que se trata de um registro em log de melhor tentativa; e a perda de rastreamento pode ocorrer. As solicitações HTTP com a autenticação "usuário" não são registradas. |
|CaptureHttpTelemetry|Bool, o padrão é true | Dinâmico | Ativar ou desativar a telemetria HTTP. A finalidade da telemetria é para que Service Fabric seja capaz de capturar dados de telemetria para ajudar a planejar o trabalho futuro e identificar áreas problemáticas. A telemetria não registra nenhum dado pessoal ou o corpo da solicitação. A telemetria captura todas as solicitações HTTP, exceto se configuradas de outra forma. |
|ClusterId |String | Dinâmico |A ID exclusiva do cluster. Isso é gerado quando o cluster é criado. |
|ConsumerInstances |String | Dinâmico |A lista de instâncias de consumidor DCA. |
|DiskFullSafetySpaceInMB |Int, o padrão é 1024 | Dinâmico |Espaço em disco restante em MB para proteger contra o uso do DCA. |
|EnableCircularTraceSession |Bool, o padrão é false | Estático |Sinalizador indica se as sessões de rastreamento circular devem ser usadas. |
|EnablePlatformEventsFileSink |Bool, o padrão é false | Estático |Habilitar/desabilitar eventos de plataforma sendo gravados no disco |
|Ativar telemetria |Bool, o padrão é true | Dinâmico |Isso vai habilitar ou desabilitar a telemetria. |
|FailuresOnlyHttpTelemetry | Bool, o padrão é false | Dinâmico | Se a captura de telemetria HTTP estiver habilitada; capturar apenas solicitações com falha. Isso é para ajudar a reduzir o número de eventos gerados para telemetria. |
|HttpTelemetryCapturePercentage | int, o padrão é 50 | Dinâmico | Se a captura de telemetria HTTP estiver habilitada; capturar apenas uma porcentagem aleatória de solicitações. Isso é para ajudar a reduzir o número de eventos gerados para telemetria. |
|MaxDiskQuotaInMB |Int, o padrão é 65536 | Dinâmico |Cota de disco em MB para arquivos de log Windows Fabric. |
|ProducerInstances |String | Dinâmico |A lista de instâncias de produtor do DCA. |

## <a name="dnsservice"></a>DnsService
| **Parâmetro** | **Valores permitidos** |**Atualizar política**| **Orientação ou descrição resumida** |
| --- | --- | --- | --- |
|EnablePartitionedQuery|bool, o padrão é FALSE|Estático|O sinalizador para habilitar o suporte para consultas DNS para serviços particionados. O recurso é desativado por padrão. Para obter mais informações, consulte [Service Fabric serviço DNS.](service-fabric-dnsservice.md)|
|InstanceCount|int, o padrão é-1|Estático|O valor padrão é-1, o que significa que DnsService está em execução em cada nó. OneBox precisa que isso seja definido como 1, já que o DnsService usa a porta 53 conhecida e, portanto, não pode ter várias instâncias no mesmo computador.|
|IsEnabled|bool, o padrão é FALSE|Estático|Habilita/desabilita DnsService. O DnsService está desabilitado por padrão e essa configuração precisa ser definida para habilitá-lo. |
|PartitionPrefix|Cadeia de caracteres, o padrão é "--"|Estático|Controla o valor de cadeia de caracteres de prefixo de partição em consultas DNS para serviços particionados. O valor: <ul><li>Deve ser compatível com RFC, pois fará parte de uma consulta DNS.</li><li>Não deve conter um ponto, '. ', pois o ponto interfere no comportamento do sufixo DNS.</li><li>Não deve ter mais de 5 caracteres.</li><li>Não pode ser uma cadeia de caracteres vazia.</li><li>Se a configuração PartitionPrefix for substituída, PartitionSuffix deverá ser substituído e vice-versa.</li></ul>Para obter mais informações, consulte [Service Fabric serviço DNS.](service-fabric-dnsservice.md)|
|PartitionSuffix|Cadeia de caracteres, o padrão é ""|Estático|Controla o valor da cadeia de caracteres de sufixo de partição em consultas DNS para serviços particionados. O valor: <ul><li>Deve ser compatível com RFC, pois fará parte de uma consulta DNS.</li><li>Não deve conter um ponto, '. ', pois o ponto interfere no comportamento do sufixo DNS.</li><li>Não deve ter mais de 5 caracteres.</li><li>Se a configuração PartitionPrefix for substituída, PartitionSuffix deverá ser substituído e vice-versa.</li></ul>Para obter mais informações, consulte [Service Fabric serviço DNS.](service-fabric-dnsservice.md) |

## <a name="eventstoreservice"></a>EventStoreService

| **Parâmetro** | **Valores permitidos** | **Atualizar política** | **Orientação ou descrição resumida** |
| --- | --- | --- | --- |
|MinReplicaSetSize|int, o padrão é 0|Estático|O MinReplicaSetSize para o serviço EventStore |
|PlacementConstraints|Cadeia de caracteres, o padrão é ""|Estático|  O PlacementConstraints para o serviço EventStore |
|TargetReplicaSetSize|int, o padrão é 0|Estático| O TargetReplicaSetSize para o serviço EventStore |

## <a name="fabricclient"></a>FabricClient

| **Parâmetro** | **Valores permitidos** | **Atualizar política** | **Orientação ou descrição resumida** |
| --- | --- | --- | --- |
|ConnectionInitializationTimeout |Tempo em segundos, o padrão é 2 |Dinâmico|Especifique TimeSpan em segundos. Intervalo de tempo limite de conexão para cada cliente de tempo tenta abrir uma conexão com o gateway.|
|HealthOperationTimeout |Tempo em segundos, o padrão é 120 |Dinâmico|Especifique TimeSpan em segundos. O tempo limite de uma mensagem de relatório enviada ao Gerenciador de integridade. |
|HealthReportRetrySendInterval |Tempo em segundos, o padrão é 30, o mínimo é 1 |Dinâmico|Especifique TimeSpan em segundos. O intervalo no qual o componente de relatório reenvia relatórios de integridade acumulados para o Gerenciador de integridade. |
|HealthReportSendInterval |Tempo em segundos, o padrão é 30 |Dinâmico|Especifique TimeSpan em segundos. O intervalo no qual o componente de relatório envia relatórios de integridade acumulados para o Gerenciador de integridade. |
|KeepAliveIntervalInSeconds |Int, o padrão é 20 |Estático|O intervalo no qual o transporte FabricClient envia mensagens Keep-Alive para o gateway. Para 0; keepAlive está desabilitada. Deve ser um valor positivo. |
|MaxFileSenderThreads |Uint, o padrão é 10 |Estático|O número máximo de arquivos que são transferidos em paralelo. |
|NodeAddresses |Cadeia de caracteres, o padrão é "" |Estático|Uma coleção de endereços (cadeias de conexão) em nós diferentes que podem ser usados para se comunicar com o Serviço de Nomenclatura. Inicialmente, o cliente se conecta selecionando um dos endereços aleatoriamente. Se mais de uma cadeia de conexão for fornecida e uma conexão falhar devido a um erro de comunicação ou tempo limite; o cliente alterna para usar o próximo endereço sequencialmente. Consulte a seção Serviço de Nomenclatura de repetição de endereço para obter detalhes sobre a semântica de novas tentativas. |
|PartitionLocationCacheLimit |Int, o padrão é 100000 |Estático|Número de partições armazenadas em cache para resolução de serviço (defina como 0 para sem limite). |
|RetryBackoffInterval |Tempo em segundos, o padrão é 3 |Dinâmico|Especifique TimeSpan em segundos. O intervalo de retirada antes de repetir a operação. |
|ServiceChangePollInterval |Tempo em segundos, o padrão é 120 |Dinâmico|Especifique TimeSpan em segundos. O intervalo entre pesquisas consecutivas para alterações de serviço do cliente para o gateway para retornos de chamada de notificações de alteração de serviço registrado. |

## <a name="fabrichost"></a>FabricHost

| **Parâmetro** | **Valores permitidos** | **Atualizar política** | **Orientação ou descrição resumida** |
| --- | --- | --- | --- |
|ActivationMaxFailureCount |Int, o padrão é 10 |Dinâmico|Essa é a contagem máxima para a qual o sistema tentará novamente a ativação com falha antes de desistir. |
|ActivationMaxRetryInterval |Tempo em segundos, o padrão é 300 |Dinâmico|Especifique TimeSpan em segundos. Intervalo máximo de repetição para ativação. Em cada falha contínua, o intervalo de repetição é calculado como min (ActivationMaxRetryInterval; Contagem de falhas contínuas * ActivationRetryBackoffInterval). |
|ActivationRetryBackoffInterval |Tempo em segundos, o padrão é 5 |Dinâmico|Especifique TimeSpan em segundos. Intervalo de retirada em cada falha de ativação; em cada falha de ativação contínua, o sistema tentará novamente a ativação até o MaxActivationFailureCount. O intervalo de repetição em cada tentativa é um produto de falha de ativação contínua e o intervalo de retirada da ativação. |
|EnableRestartManagement |Bool, o padrão é false |Dinâmico|Isso é para habilitar a reinicialização do servidor. |
|EnableServiceFabricAutomaticUpdates |Bool, o padrão é false |Dinâmico|Isso é para habilitar a atualização automática da malha via Windows Update. |
|EnableServiceFabricBaseUpgrade |Bool, o padrão é false |Dinâmico|Isso é para habilitar a atualização básica para o servidor. |
|FailureReportingExpeditedReportingIntervalEnabled | Bool, o padrão é true | Estático | Permite taxas de carregamento mais rápidas em DCA quando FabricHost está em modo de relatório de falha. |
|FailureReportingTimeout | TimeSpan, o padrão é Common:: TimeSpan:: FromSeconds (60) | Estático |Especifique TimeSpan em segundos. Tempo limite para relatório de falha DCA no caso de FabricHost encontrar uma falha na inicialização do estágio inicial. | 
|RunDCAOnStartupFailure | Bool, o padrão é true | Estático |Determina se o DCA deve ser iniciado para carregar logs quando enfrentados problemas de inicialização no FabricHost. | 
|StartTimeout |Tempo em segundos, o padrão é 300 |Dinâmico|Especifique TimeSpan em segundos. Tempo limite para a inicialização do fabricactivationmanager. |
|StopTimeout |Tempo em segundos, o padrão é 300 |Dinâmico|Especifique TimeSpan em segundos. O tempo limite para ativação do serviço hospedado; desativação e atualização. |

## <a name="fabricnode"></a>FabricNode

| **Parâmetro** | **Valores permitidos** | **Atualizar política** | **Orientação ou descrição resumida** |
| --- | --- | --- | --- |
|ClientAuthX509FindType |Cadeia de caracteres, o padrão é "FindByThumbprint" |Dinâmico|Indica como pesquisar o certificado no repositório especificado pelo valor com suporte ClientAuthX509StoreName: FindByThumbprint; FindBySubjectName. |
|ClientAuthX509FindValue |Cadeia de caracteres, o padrão é "" | Dinâmico|Valor do filtro de pesquisa usado para localizar o certificado para a função de administrador padrão FabricClient. |
|ClientAuthX509FindValueSecondary |Cadeia de caracteres, o padrão é "" |Dinâmico|Valor do filtro de pesquisa usado para localizar o certificado para a função de administrador padrão FabricClient. |
|ClientAuthX509StoreName |Cadeia de caracteres, o padrão é "My" |Dinâmico|Nome do repositório de certificados X. 509 que contém o certificado para a função de administrador padrão FabricClient. |
|ClusterX509FindType |Cadeia de caracteres, o padrão é "FindByThumbprint" |Dinâmico|Indica como pesquisar o certificado de cluster no repositório especificado por ClusterX509StoreName valores com suporte: "FindByThumbprint"; "FindBySubjectName" com "FindBySubjectName"; Quando há várias correspondências; Aquele com a expiração mais distante é usado. |
|ClusterX509FindValue |Cadeia de caracteres, o padrão é "" |Dinâmico|Valor do filtro de pesquisa usado para localizar o certificado do cluster. |
|ClusterX509FindValueSecondary |Cadeia de caracteres, o padrão é "" |Dinâmico|Valor do filtro de pesquisa usado para localizar o certificado do cluster. |
|ClusterX509StoreName |Cadeia de caracteres, o padrão é "My" |Dinâmico|Nome do repositório de certificados X. 509 que contém o certificado de cluster para proteger a comunicação dentro do cluster. |
|EndApplicationPortRange |Int, o padrão é 0 |Estático|End (não inclusivo) das portas de aplicativo gerenciadas pelo subsistema de hospedagem. Obrigatório se EndpointFilteringEnabled for true na hospedagem. |
|ServerAuthX509FindType |Cadeia de caracteres, o padrão é "FindByThumbprint" |Dinâmico|Indica como pesquisar o certificado do servidor no repositório especificado pelo valor com suporte ServerAuthX509StoreName: FindByThumbprint; FindBySubjectName. |
|ServerAuthX509FindValue |Cadeia de caracteres, o padrão é "" |Dinâmico|Valor do filtro de pesquisa usado para localizar o certificado do servidor. |
|ServerAuthX509FindValueSecondary |Cadeia de caracteres, o padrão é "" |Dinâmico|Valor do filtro de pesquisa usado para localizar o certificado do servidor. |
|ServerAuthX509StoreName |Cadeia de caracteres, o padrão é "My" |Dinâmico|Nome do repositório de certificados X. 509 que contém o certificado do servidor para o serviço entrée. |
|StartApplicationPortRange |Int, o padrão é 0 |Estático|Início das portas de aplicativo gerenciadas pelo subsistema de hospedagem. Obrigatório se EndpointFilteringEnabled for true na hospedagem. |
|StateTraceInterval |Tempo em segundos, o padrão é 300 |Estático|Especifique TimeSpan em segundos. O intervalo para rastrear o status do nó em cada nó e os nós em FM/FMM. |
|UserRoleClientX509FindType |Cadeia de caracteres, o padrão é "FindByThumbprint" |Dinâmico|Indica como pesquisar o certificado no repositório especificado pelo valor com suporte UserRoleClientX509StoreName: FindByThumbprint; FindBySubjectName. |
|UserRoleClientX509FindValue |Cadeia de caracteres, o padrão é "" |Dinâmico|Valor do filtro de pesquisa usado para localizar o certificado para a função de usuário padrão FabricClient. |
|UserRoleClientX509FindValueSecondary |Cadeia de caracteres, o padrão é "" |Dinâmico|Valor do filtro de pesquisa usado para localizar o certificado para a função de usuário padrão FabricClient. |
|UserRoleClientX509StoreName |Cadeia de caracteres, o padrão é "My" |Dinâmico|Nome do repositório de certificados X. 509 que contém o certificado para a função de usuário padrão FabricClient. |

## <a name="failovermanager"></a>FailoverManager

| **Parâmetro** | **Valores permitidos** | **Atualizar política** | **Orientação ou descrição resumida** |
| --- | --- | --- | --- |
|AllowNodeStateRemovedForSeedNode|Bool, o padrão é FALSE |Dinâmico|Sinalizador para indicar se é permitido remover o estado do nó de um nó semente |
|BuildReplicaTimeLimit|TimeSpan, o padrão é Common:: TimeSpan:: FromSeconds (3600)|Dinâmico|Especifique TimeSpan em segundos. O limite de tempo para a criação de uma réplica com estado; após o qual um relatório de integridade de aviso será iniciado |
|ClusterPauseThreshold|int, o padrão é 1|Dinâmico|Se o número de nós no sistema ficar abaixo desse valor, então o posicionamento; balanceamento de carga; e o failover é interrompido. |
|CreateInstanceTimeLimit|TimeSpan, o padrão é Common:: TimeSpan:: FromSeconds (300)|Dinâmico|Especifique TimeSpan em segundos. O limite de tempo para a criação de uma instância sem estado; após o qual um relatório de integridade de aviso será iniciado |
|ExpectedClusterSize|int, o padrão é 1|Dinâmico|Quando o cluster é inicialmente iniciado; o FM aguardará que esse número de nós se reporte antes de começar a colocar outros serviços; incluindo os serviços do sistema como nomenclatura. Aumentar esse valor aumenta o tempo que leva um cluster para ser inicializado; mas impede que os nós iniciais fiquem sobrecarregados e também as movimentações adicionais que serão necessárias à medida que mais nós ficarem online. Esse valor geralmente deve ser definido como uma pequena fração do tamanho inicial do cluster. |
|ExpectedNodeDeactivationDuration|TimeSpan, o padrão é Common:: TimeSpan:: FromSeconds (60,0 \* 30)|Dinâmico|Especifique TimeSpan em segundos. Essa é a duração esperada para um nó concluir a desativação no. |
|ExpectedNodeFabricUpgradeDuration|TimeSpan, o padrão é Common:: TimeSpan:: FromSeconds (60,0 \* 30)|Dinâmico|Especifique TimeSpan em segundos. Essa é a duração esperada para que um nó seja atualizado durante a atualização Windows Fabric. |
|ExpectedReplicaUpgradeDuration|TimeSpan, o padrão é Common:: TimeSpan:: FromSeconds (60,0 \* 30)|Dinâmico|Especifique TimeSpan em segundos. Essa é a duração esperada para que todas as réplicas sejam atualizadas em um nó durante a atualização do aplicativo. |
|IsSingletonReplicaMoveAllowedDuringUpgrade|bool, o padrão é TRUE|Dinâmico|Se definido como true; as réplicas com um tamanho de conjunto de réplicas de destino de 1 terão permissão para serem movidas durante a atualização. |
|MinReplicaSetSize|int, o padrão é 3|Não permitido|Esse é o tamanho mínimo do conjunto de réplicas para o FM. Se o número de réplicas ativas do FM cair abaixo desse valor; o FM rejeitará as alterações no cluster até que pelo menos o número mínimo de réplicas seja recuperado |
|PlacementConstraints|Cadeia de caracteres, o padrão é ""|Não permitido|Quaisquer restrições de posicionamento para as réplicas do Gerenciador de failover |
|PlacementTimeLimit|TimeSpan, o padrão é Common:: TimeSpan:: FromSeconds (600)|Dinâmico|Especifique TimeSpan em segundos. O limite de tempo para atingir a contagem de réplica de destino; após o qual um relatório de integridade de aviso será iniciado |
|QuorumLossWaitDuration |Tempo em segundos, o padrão é MaxValue |Dinâmico|Especifique TimeSpan em segundos. Essa é a duração máxima para a qual permitimos que uma partição esteja em um estado de perda de quorum. Se a partição ainda estiver em perda de quorum após essa duração; a partição é recuperada da perda de quorum considerando as réplicas inativas como perdidas. Observe que isso pode potencialmente causar perda de dados. |
|ReconfigurationTimeLimit|TimeSpan, o padrão é Common:: TimeSpan:: FromSeconds (300)|Dinâmico|Especifique TimeSpan em segundos. O limite de tempo para reconfiguração; após o qual um relatório de integridade de aviso será iniciado |
|ReplicaRestartWaitDuration|TimeSpan, o padrão é Common:: TimeSpan:: FromSeconds (60,0 \* 30)|Não permitido|Especifique TimeSpan em segundos. Este é o ReplicaRestartWaitDuration para o FMService |
| SeedNodeQuorumAdditionalBufferNodes | int, o padrão é 0 | Dinâmico | Buffer de nós de semente que é necessário para estar ativo (junto com o quorum de nós de semente) a FM deve permitir que um máximo de nós de semente (totalNumSeedNodes-(seedNodeQuorum + SeedNodeQuorumAdditionalBufferNodes)) fique inativo. |
|StandByReplicaKeepDuration|TimeSpan, o padrão é Common:: TimeSpan:: FromSeconds (3600,0 \* 24 \* 7)|Não permitido|Especifique TimeSpan em segundos. Este é o StandByReplicaKeepDuration para o FMService |
|TargetReplicaSetSize|int, o padrão é 7|Não permitido|Esse é o número de destino de réplicas de FM que Windows Fabric será mantido. Um número mais alto resulta em maior confiabilidade dos dados de FM; com uma pequena compensação de desempenho. |
|UserMaxStandByReplicaCount |Int, o padrão é 1 |Dinâmico|O número máximo padrão de réplicas em espera que o sistema mantém para os serviços do usuário. |
|UserReplicaRestartWaitDuration |Tempo em segundos, o padrão é 60,0 \* 30 |Dinâmico|Especifique TimeSpan em segundos. Quando uma réplica persistente fica inativa; Windows Fabric aguarda essa duração para que a réplica volte a ser reativada antes de criar novas réplicas de substituição (o que exigiria uma cópia do estado). |
|UserStandByReplicaKeepDuration |Tempo em segundos, o padrão é 3600,0 \* 24 \* 7 |Dinâmico|Especifique TimeSpan em segundos. Quando uma réplica persistente retorna de um estado inativo; Ele pode já ter sido substituído. Esse temporizador determina por quanto tempo o FM manterá a réplica em espera antes de descartar. |

## <a name="faultanalysisservice"></a>FaultAnalysisService

| **Parâmetro** | **Valores permitidos** | **Atualizar política** | **Orientação ou descrição resumida** |
| --- | --- | --- | --- |
|CompletedActionKeepDurationInSeconds | Int, o padrão é 604800 |Estático| Isso é aproximadamente por quanto tempo manter as ações que estão em um estado terminal. Isso também depende do StoredActionCleanupIntervalInSeconds; como o trabalho para limpeza só é feito nesse intervalo. 604800 é de 7 dias. |
|DataLossCheckPollIntervalInSeconds|int, o padrão é 5|Estático|Esse é o tempo entre as verificações que o sistema executa enquanto aguarda a perda de dados acontecer. O número de vezes que o número de perda de dados será verificado por iteração interna é DataLossCheckWaitDurationInSeconds/this. |
|DataLossCheckWaitDurationInSeconds|int, o padrão é 25|Estático|A quantidade total de tempo; em segundos; o sistema aguardará a perda de dados. Isso é usado internamente quando a API StartPartitionDataLossAsync () é chamada. |
|MinReplicaSetSize |Int, o padrão é 0 |Estático|O MinReplicaSetSize para FaultAnalysisService. |
|PlacementConstraints | Cadeia de caracteres, o padrão é ""|Estático| O PlacementConstraints para FaultAnalysisService. |
|QuorumLossWaitDuration | Tempo em segundos, o padrão é MaxValue |Estático|Especifique TimeSpan em segundos. O QuorumLossWaitDuration para FaultAnalysisService. |
|ReplicaDropWaitDurationInSeconds|int, o padrão é 600|Estático|Esse parâmetro é usado quando a API de perda de dados é chamada. Ele controla por quanto tempo o sistema aguardará que uma réplica seja descartada depois que a réplica for invocada internamente. |
|ReplicaRestartWaitDuration |Tempo em segundos, o padrão é 60 minutos|Estático|Especifique TimeSpan em segundos. O ReplicaRestartWaitDuration para FaultAnalysisService. |
|StandByReplicaKeepDuration| Tempo em segundos, o padrão é (60*24*7) minutos |Estático|Especifique TimeSpan em segundos. O StandByReplicaKeepDuration para FaultAnalysisService. |
|StoredActionCleanupIntervalInSeconds | Int, o padrão é 3600 |Estático|É com que frequência o repositório será limpo. Somente ações em um estado de terminal; e a conclusão de pelo menos CompletedActionKeepDurationInSeconds atrás será removida. |
|StoredChaosEventCleanupIntervalInSeconds | Int, o padrão é 3600 |Estático|É com que frequência a loja será auditada para limpeza; Se o número de eventos for maior que 30000; a limpeza será iniciada. |
|TargetReplicaSetSize |Int, o padrão é 0 |Estático|NOT_PLATFORM_UNIX_START o TargetReplicaSetSize para FaultAnalysisService. |

## <a name="federation"></a>de Federação

| **Parâmetro** | **Valores permitidos** | **Atualizar política** | **Orientação ou descrição resumida** |
| --- | --- | --- | --- |
|LeaseDuration |Tempo em segundos, o padrão é 30 |Dinâmico|Duração em que uma concessão dura entre um nó e seus vizinhos. |
|LeaseDurationAcrossFaultDomain |Tempo em segundos, o padrão é 30 |Dinâmico|Duração em que uma concessão dura entre um nó e seus vizinhos entre domínios de falha. |

## <a name="filestoreservice"></a>FileStoreService

| **Parâmetro** | **Valores permitidos** | **Atualizar política** | **Orientação ou descrição resumida** |
| --- | --- | --- | --- |
|AcceptChunkUpload|Bool, o padrão é TRUE|Dinâmico|Configuração para determinar se o serviço de repositório de arquivos aceita carregamento de arquivo baseado em partes ou não durante a cópia do pacote de aplicativos. |
|AnonymousAccessEnabled | Bool, o padrão é true |Estático|Habilitar/desabilitar o acesso anônimo aos compartilhamentos FileStoreService. |
|CommonName1Ntlmx509CommonName|Cadeia de caracteres, o padrão é ""|Estático| O nome comum do certificado X509 usado para gerar HMAC no CommonName1NtlmPasswordSecret ao usar a autenticação NTLM |
|CommonName1Ntlmx509StoreLocation|Cadeia de caracteres, o padrão é "LocalMachine"|Estático|O local de armazenamento do certificado X509 usado para gerar HMAC no CommonName1NtlmPasswordSecret ao usar a autenticação NTLM |
|CommonName1Ntlmx509StoreName|Cadeia de caracteres, o padrão é "MY"| Estático|O nome do repositório do certificado X509 usado para gerar HMAC no CommonName1NtlmPasswordSecret ao usar a autenticação NTLM |
|CommonName2Ntlmx509CommonName|Cadeia de caracteres, o padrão é ""|Estático|O nome comum do certificado X509 usado para gerar HMAC no CommonName2NtlmPasswordSecret ao usar a autenticação NTLM |
|CommonName2Ntlmx509StoreLocation|Cadeia de caracteres, o padrão é "LocalMachine"| Estático|O local de armazenamento do certificado X509 usado para gerar HMAC no CommonName2NtlmPasswordSecret ao usar a autenticação NTLM |
|CommonName2Ntlmx509StoreName|Cadeia de caracteres, o padrão é "MY"|Estático| O nome do repositório do certificado X509 usado para gerar HMAC no CommonName2NtlmPasswordSecret ao usar a autenticação NTLM |
|CommonNameNtlmPasswordSecret|SecureString, o padrão é Common:: SecureString ("")| Estático|O segredo de senha usado como semente para gerar a mesma senha ao usar a autenticação NTLM |
|DiskSpaceHealthReportingIntervalWhenCloseToOutOfDiskSpace |TimeSpan, o padrão é Common:: TimeSpan:: FromMinutes (5)|Dinâmico|Especifique TimeSpan em segundos. O intervalo de tempo entre a verificação do espaço em disco para relatar o evento de integridade quando o disco está perto de ficar sem espaço. |
|DiskSpaceHealthReportingIntervalWhenEnoughDiskSpace |TimeSpan, o padrão é Common:: TimeSpan:: FromMinutes (15)|Dinâmico|Especifique TimeSpan em segundos. O intervalo de tempo entre a verificação de espaço em disco para relatar o evento de integridade quando houver espaço suficiente no disco. |
|EnableImageStoreHealthReporting |bool, o padrão é TRUE |Estático|Configuração para determinar se o serviço de repositório de arquivos deve relatar sua integridade. |
|FreeDiskSpaceNotificationSizeInKB|Int64, o padrão é 25\*1024 |Dinâmico|O tamanho do espaço livre em disco abaixo do qual pode ocorrer o aviso de integridade. O valor mínimo dessa configuração e do FreeDiskSpaceNotificationThresholdPercentage config são usados para determinar o envio do aviso de integridade. |
|FreeDiskSpaceNotificationThresholdPercentage|duplo, o padrão é 0, 2 |Dinâmico|A porcentagem de espaço livre em disco abaixo do qual pode ocorrer o aviso de integridade. O valor mínimo dessa configuração e do FreeDiskSpaceNotificationInMB config são usados para determinar o envio de aviso de integridade. |
|GenerateV1CommonNameAccount| bool, o padrão é TRUE|Estático|Especifica se uma conta com algoritmo de geração de nome de usuário v1 deve ser gerada. A partir do Service Fabric versão 6,1; uma conta com geração V2 é sempre criada. A conta v1 é necessária para atualizações de/para versões que não dão suporte à geração v2 (antes de 6,1).|
|MaxCopyOperationThreads | Uint, o padrão é 0 |Dinâmico| O número máximo de arquivos paralelos que o secundário pode copiar do primário. ' 0 ' = = número de núcleos. |
|MaxFileOperationThreads | Uint, o padrão é 100 |Estático| O número máximo de threads paralelos com permissão para executar FileOperations (copiar/mover) no primário. ' 0 ' = = número de núcleos. |
|MaxRequestProcessingThreads | Uint, o padrão é 200 |Estático|O número máximo de threads paralelos com permissão para processar solicitações no primário. ' 0 ' = = número de núcleos. |
|MaxSecondaryFileCopyFailureThreshold | Uint, o padrão é 25|Dinâmico|O número máximo de repetições de cópia de arquivo no secundário antes de desistir. |
|MaxStoreOperations | Uint, o padrão é 4096 |Estático|O número máximo de operações de transação de repositório paralelas permitidas no primário. ' 0 ' = = número de núcleos. |
|NamingOperationTimeout |Tempo em segundos, o padrão é 60 |Dinâmico|Especifique TimeSpan em segundos. O tempo limite para a execução da operação de nomenclatura. |
|PrimaryAccountNTLMPasswordSecret | SecureString, o padrão é vazio |Estático| O segredo de senha usado como semente para gerar a mesma senha ao usar a autenticação NTLM. |
|PrimaryAccountNTLMX509StoreLocation | Cadeia de caracteres, o padrão é "LocalMachine"|Estático| O local de armazenamento do certificado X509 usado para gerar HMAC no PrimaryAccountNTLMPasswordSecret ao usar a autenticação NTLM. |
|PrimaryAccountNTLMX509StoreName | Cadeia de caracteres, o padrão é "MY"|Estático| O nome do repositório do certificado X509 usado para gerar HMAC no PrimaryAccountNTLMPasswordSecret ao usar a autenticação NTLM. |
|PrimaryAccountNTLMX509Thumbprint | Cadeia de caracteres, o padrão é ""|Estático|A impressão digital do certificado X509 usado para gerar HMAC no PrimaryAccountNTLMPasswordSecret ao usar a autenticação NTLM. |
|PrimaryAccountType | Cadeia de caracteres, o padrão é "" |Estático|O AccountType primário do principal para a ACL dos compartilhamentos FileStoreService. |
|PrimaryAccountUserName | Cadeia de caracteres, o padrão é "" |Estático|O nome de usuário da conta primária da entidade para a ACL dos compartilhamentos FileStoreService. |
|PrimaryAccountUserPassword | SecureString, o padrão é vazio |Estático|A senha da conta primária da entidade para a ACL dos compartilhamentos FileStoreService. |
|QueryOperationTimeout | Tempo em segundos, o padrão é 60 |Dinâmico|Especifique TimeSpan em segundos. O tempo limite para executar a operação de consulta. |
|SecondaryAccountNTLMPasswordSecret | SecureString, o padrão é vazio |Estático| O segredo de senha usado como semente para gerar a mesma senha ao usar a autenticação NTLM. |
|SecondaryAccountNTLMX509StoreLocation | Cadeia de caracteres, o padrão é "LocalMachine" |Estático|O local de armazenamento do certificado X509 usado para gerar HMAC no SecondaryAccountNTLMPasswordSecret ao usar a autenticação NTLM. |
|SecondaryAccountNTLMX509StoreName | Cadeia de caracteres, o padrão é "MY" |Estático|O nome do repositório do certificado X509 usado para gerar HMAC no SecondaryAccountNTLMPasswordSecret ao usar a autenticação NTLM. |
|SecondaryAccountNTLMX509Thumbprint | Cadeia de caracteres, o padrão é ""| Estático|A impressão digital do certificado X509 usado para gerar HMAC no SecondaryAccountNTLMPasswordSecret ao usar a autenticação NTLM. |
|SecondaryAccountType | Cadeia de caracteres, o padrão é ""|Estático| O AccountType secundário do principal para a ACL dos compartilhamentos FileStoreService. |
|SecondaryAccountUserName | Cadeia de caracteres, o padrão é ""| Estático|O nome de usuário da conta secundária da entidade para a ACL dos compartilhamentos FileStoreService. |
|SecondaryAccountUserPassword | SecureString, o padrão é vazio |Estático|A senha da conta secundária da entidade para a ACL dos compartilhamentos FileStoreService. |
|SecondaryFileCopyRetryDelayMilliseconds|uint, o padrão é 500|Dinâmico|O atraso de repetição de cópia de arquivo (em milissegundos).|
|UseChunkContentInTransportMessage|bool, o padrão é TRUE|Dinâmico|O sinalizador para usar a nova versão do protocolo de carregamento introduzido na v 6.4. Essa versão de protocolo usa o transporte do Service Fabric para carregar arquivos no repositório de imagens, o que fornece um desempenho melhor do que o protocolo SMB usado em versões anteriores. |

## <a name="healthmanager"></a>HealthManager

| **Parâmetro** | **Valores permitidos** | **Atualizar política** | **Orientação ou descrição resumida** |
| --- | --- | --- | --- |
|EnableApplicationTypeHealthEvaluation |Bool, o padrão é false |Estático|Política de avaliação de integridade do cluster: habilitar a avaliação de integridade do tipo de aplicativo. |
|MaxSuggestedNumberOfEntityHealthReports|Int, o padrão é 100 |Dinâmico|O número máximo de relatórios de integridade que uma entidade pode ter antes de gerar preocupações sobre a lógica de relatório de integridade do Watchdog. Cada entidade de integridade deve ter um número relativamente pequeno de relatórios de integridade. Se a contagem de relatórios ficar acima desse número; pode haver problemas com a implementação do Watchdog. Uma entidade com muitos relatórios é sinalizada por meio de um relatório de integridade de aviso quando a entidade é avaliada. |

## <a name="healthmanagerclusterhealthpolicy"></a>HealthManager/ClusterHealthPolicy

| **Parâmetro** | **Valores permitidos** | **Atualizar política** | **Orientação ou descrição resumida** |
| --- | --- | --- | --- |
|ConsiderWarningAsError |Bool, o padrão é false |Estático|Política de avaliação de integridade do cluster: os avisos são tratados como erros. |
|MaxPercentUnhealthyApplications | Int, o padrão é 0 |Estático|Política de avaliação de integridade do cluster: porcentagem máxima de aplicativos não íntegros permitidos para que o cluster seja íntegro. |
|MaxPercentUnhealthyNodes | Int, o padrão é 0 |Estático|Política de avaliação de integridade do cluster: o percentual máximo de nós não íntegros permitidos para o cluster ser íntegro. |

## <a name="healthmanagerclusterupgradehealthpolicy"></a>HealthManager/ClusterUpgradeHealthPolicy

| **Parâmetro** | **Valores permitidos** | **Atualizar política** | **Orientação ou descrição resumida** |
| --- | --- | --- | --- |
|MaxPercentDeltaUnhealthyNodes|int, o padrão é 10|Estático|Política de avaliação de integridade da atualização do cluster: porcentagem máxima de nós Delta não íntegros permitidos para o cluster estar íntegro |
|MaxPercentUpgradeDomainDeltaUnhealthyNodes|int, o padrão é 15|Estático|Política de avaliação de integridade da atualização do cluster: porcentagem máxima de Delta de nós não íntegros em um domínio de atualização permitido para o cluster ser íntegro |

## <a name="hosting"></a>Alojamento

| **Parâmetro** | **Valores permitidos** | **Atualizar política** | **Orientação ou descrição resumida** |
| --- | --- | --- | --- |
|ActivationMaxFailureCount |Número inteiro, o padrão é 10 |Dinâmico|Número de vezes que o sistema tenta a ativação com falha antes de desistir |
|ActivationMaxRetryInterval |Tempo em segundos, o padrão é 300 |Dinâmico|Em cada falha de ativação contínua, o sistema repete a ativação para até ActivationMaxFailureCount. ActivationMaxRetryInterval especifica o intervalo de tempo de espera antes de tentar novamente após cada falha de ativação |
|ActivationRetryBackoffInterval |Tempo em segundos, o padrão é 5 |Dinâmico|Intervalo de retirada em todas as falhas de ativação; Em cada falha de ativação contínua, o sistema tenta novamente a ativação até o MaxActivationFailureCount. O intervalo de repetição em cada tentativa é um produto de falha de ativação contínua e o intervalo de retirada da ativação. |
|ActivationTimeout| TimeSpan, o padrão é Common:: TimeSpan:: FromSeconds (180)|Dinâmico| Especifique TimeSpan em segundos. O tempo limite para ativação do aplicativo; desativação e atualização. |
|ApplicationHostCloseTimeout| TimeSpan, o padrão é Common:: TimeSpan:: FromSeconds (120)|Dinâmico| Especifique TimeSpan em segundos. Quando a saída da malha é detectada em processos autoativados; FabricRuntime fecha todas as réplicas no processo host do usuário (ApplicationHost). Este é o tempo limite para a operação de fechamento. |
| CnsNetworkPluginCnmUrlPort | wstring, o padrão é L "48080" | Estático | Porta de URL da API do Azure CNM |
| CnsNetworkPluginCnsUrlPort | wstring, o padrão é L "10090" | Estático | Porta de URL do Azure CNS |
|ContainerServiceArguments|Cadeia de caracteres, o padrão é "-H localhost: 2375-H npipe://"|Estático|Service Fabric (IT) gerencia o daemon do Docker (exceto em computadores cliente Windows, como Win10). Essa configuração permite que o usuário especifique argumentos personalizados que devem ser passados para o daemon do Docker ao iniciá-lo. Quando argumentos personalizados são especificados, Service Fabric não passam nenhum outro argumento para o mecanismo do Docker, exceto o argumento '--pidfile '. Portanto, os usuários não devem especificar o argumento '--pidfile ' como parte de seus argumentos de cliente. Além disso, os argumentos personalizados devem garantir que o daemon do Docker escute o pipe de nome padrão no Windows (ou no soquete de domínio do UNIX no Linux) para que Service Fabric possa se comunicar com ele.|
|ContainerServiceLogFileMaxSizeInKb|int, o padrão é 32768|Estático|Tamanho máximo do arquivo de log gerado pelos contêineres do Docker.  Somente Windows.|
|ContainerImageDownloadTimeout|int, número de segundos, o padrão é 1200 (20 min)|Dinâmico|Número de segundos antes do download da imagem expirar.|
|ContainerImagesToSkip|Cadeia de caracteres, nomes de imagem separados por caractere de linha vertical, o padrão é ""|Estático|Nome de uma ou mais imagens de contêiner que não devem ser excluídas.  Usado com o parâmetro PruneContainerImages.|
|ContainerServiceLogFileNamePrefix|Cadeia de caracteres, o padrão é "sfcontainerlogs"|Estático|Prefixo de nome de arquivo para arquivos de log gerados por contêineres do Docker.  Somente Windows.|
|ContainerServiceLogFileRetentionCount|int, o padrão é 10|Estático|Número de arquivos de log gerados por contêineres do Docker antes que os arquivos de log sejam substituídos.  Somente Windows.|
|CreateFabricRuntimeTimeout|TimeSpan, o padrão é Common:: TimeSpan:: FromSeconds (120)|Dinâmico| Especifique TimeSpan em segundos. O valor de tempo limite para a chamada FabricCreateRuntime de sincronização |
|DefaultContainerRepositoryAccountName|Cadeia de caracteres, o padrão é ""|Estático|Credenciais padrão usadas em vez de credenciais especificadas em ApplicationManifest. xml |
|DefaultContainerRepositoryPassword|Cadeia de caracteres, o padrão é ""|Estático|Credenciais de senha padrão usadas em vez de credenciais especificadas em ApplicationManifest. xml|
|DefaultContainerRepositoryPasswordType|Cadeia de caracteres, o padrão é ""|Estático|Quando não for uma cadeia de caracteres vazia, o valor poderá ser "Encrypted" ou "SecretsStoreRef".|
|DefaultDnsSearchSuffixEmpty|bool, o padrão é FALSE|Estático|Por padrão, o nome do serviço é anexado ao nome DNS da it para serviços de contêiner. Esse recurso interrompe esse comportamento para que nada seja acrescentado ao nome DNS da it por padrão no caminho de resolução.|
|DeploymentMaxFailureCount|int, o padrão é 20| Dinâmico|A implantação do aplicativo será repetida por DeploymentMaxFailureCount vezes antes de falhar a implantação desse aplicativo no nó.| 
|DeploymentMaxRetryInterval| TimeSpan, o padrão é Common:: TimeSpan:: FromSeconds (3600)|Dinâmico| Especifique TimeSpan em segundos. Intervalo máximo de repetição para a implantação. Em cada falha contínua, o intervalo de repetição é calculado como min (DeploymentMaxRetryInterval; Contagem de falhas contínuas * DeploymentRetryBackoffInterval) |
|DeploymentRetryBackoffInterval| TimeSpan, o padrão é Common:: TimeSpan:: FromSeconds (10)|Dinâmico|Especifique TimeSpan em segundos. Intervalo de retirada para a falha de implantação. Em cada falha de implantação contínua, o sistema tentará novamente a implantação até o MaxDeploymentFailureCount. O intervalo de repetição é um produto de falha de implantação contínua e o intervalo de retirada da implantação. |
|DisableContainers|bool, o padrão é FALSE|Estático|Configuração para desabilitar contêineres – usado em vez de DisableContainerServiceStartOnContainerActivatorOpen que é uma configuração preterida |
|DisableDockerRequestRetry|bool, o padrão é FALSE |Dinâmico| Por padrão, o it se comunica com DD (Docker Dameon) com um tempo limite de "DockerRequestTimeout" para cada solicitação HTTP enviada a ele. Se DD não responder dentro desse período de tempo; It reenviará a solicitação se a operação de nível superior ainda tiver o tempo restante.  Com o contêiner HyperV; O DD às vezes leva muito mais tempo para exibir o contêiner ou desativá-lo. Nesses casos, DD a solicitação atinge o tempo limite da perspectiva do it e tenta a operação novamente. Às vezes, isso parece adicionar mais pressão ao DD. Essa configuração permite desabilitar essa repetição e aguardar que o DD responda. |
|DnsServerListTwoIps | Bool, o padrão é FALSE | Estático | Esses sinalizadores adicionam o servidor DNS local duas vezes para ajudar a aliviar problemas de resolução intermitente. |
| DoNotInjectLocalDnsServer | bool, o padrão é FALSE | Estático | Impede que o tempo de execução Insira o IP local como servidor DNS para contêineres. |
|EnableActivateNoWindow| bool, o padrão é FALSE|Dinâmico| O processo ativado é criado em segundo plano sem qualquer console. |
|EnableContainerServiceDebugMode|bool, o padrão é TRUE|Estático|Habilitar/desabilitar registro em log para contêineres do Docker.  Somente Windows.|
|EnableDockerHealthCheckIntegration|bool, o padrão é TRUE|Estático|Habilita a integração de eventos do Docker HEALTHCHECK com Service Fabric relatório de integridade do sistema |
|EnableProcessDebugging|bool, o padrão é FALSE|Dinâmico| Habilita a inicialização de hosts de aplicativo sob o depurador |
|EndpointProviderEnabled| bool, o padrão é FALSE|Estático| Habilita o gerenciamento de recursos de ponto de extremidade por malha. Requer a especificação do intervalo de portas de aplicativo inicial e final em FabricNode. |
|FabricContainerAppsEnabled| bool, o padrão é FALSE|Estático| |
|FirewallPolicyEnabled|bool, o padrão é FALSE|Estático| Habilita a abertura de portas de firewall para recursos de ponto de extremidade com portas explícitas especificadas no manifesto |
|GetCodePackageActivationContextTimeout|TimeSpan, o padrão é Common:: TimeSpan:: FromSeconds (120)|Dinâmico|Especifique TimeSpan em segundos. O valor de tempo limite para as chamadas CodePackageActivationContext. Isso não é aplicável aos serviços ad hoc. |
|GovernOnlyMainMemoryForProcesses|bool, o padrão é FALSE|Estático|O comportamento padrão da governança de recursos é colocar o limite especificado em MemoryInMB na quantidade de memória total (RAM + permuta) que o processo usa. Se o limite for excedido; o processo receberá a exceção de OutOfMemory. Se esse parâmetro for definido como true; o limite será aplicado somente à quantidade de memória RAM que um processo usará. Se esse limite for excedido; e se essa configuração for verdadeira; em seguida, o sistema operacional vai trocar a memória principal em disco. |
|IPProviderEnabled|bool, o padrão é FALSE|Estático|Habilita o gerenciamento de endereços IP. |
|IsDefaultContainerRepositoryPasswordEncrypted|bool, o padrão é FALSE|Estático|Se o DefaultContainerRepositoryPassword está criptografado ou não.|
|LinuxExternalExecutablePath|Cadeia de caracteres, o padrão é "/usr/bin/" |Estático|O diretório primário de comandos executáveis externos no nó.|
|NTLMAuthenticationEnabled|bool, o padrão é FALSE|Estático| Habilita o suporte para o uso de NTLM pelos pacotes de código que estão sendo executados como outros usuários para que os processos entre computadores possam se comunicar com segurança. |
|NTLMAuthenticationPasswordSecret|SecureString, o padrão é Common:: SecureString ("")|Estático|É um criptografado que é usado para gerar a senha para usuários NTLM. Deve ser definido se NTLMAuthenticationEnabled for verdadeiro. Validado pelo implantador. |
|NTLMSecurityUsersByX509CommonNamesRefreshInterval|TimeSpan, o padrão é Common:: TimeSpan:: FromMinutes (3)|Dinâmico|Especifique TimeSpan em segundos. Configurações específicas do ambiente o intervalo periódico no qual a hospedagem verifica se há novos certificados a serem usados para a configuração do FileStoreService NTLM. |
|NTLMSecurityUsersByX509CommonNamesRefreshTimeout|TimeSpan, o padrão é Common:: TimeSpan:: FromMinutes (4)|Dinâmico| Especifique TimeSpan em segundos. O tempo limite para configurar usuários NTLM usando nomes comuns de certificado. Os usuários NTLM são necessários para compartilhamentos de FileStoreService. |
|PruneContainerImages|bool, o padrão é FALSE|Dinâmico| Remova as imagens de contêiner de aplicativo não usadas dos nós. Quando um ApplicationType tem o registro cancelado no Cluster Service Fabric, as imagens de contêiner que foram usadas por esse aplicativo serão removidas em nós onde ele foi baixado pelo Service Fabric. A remoção é executada a cada hora, portanto, pode levar até uma hora (mais tempo para remover a imagem) para que as imagens sejam removidas do cluster.<br>Service Fabric nunca baixará ou removerá imagens não relacionadas a um aplicativo.  Imagens não relacionadas que foram baixadas manualmente ou, de outra forma, devem ser removidas explicitamente.<br>As imagens que não devem ser excluídas podem ser especificadas no parâmetro ContainerImagesToSkip.| 
|RegisterCodePackageHostTimeout|TimeSpan, o padrão é Common:: TimeSpan:: FromSeconds (120)|Dinâmico| Especifique TimeSpan em segundos. O valor de tempo limite para a chamada de sincronização FabricRegisterCodePackageHost. Isso é aplicável somente a hosts de aplicativo de pacote de vários códigos como FWP |
|RequestTimeout|TimeSpan, o padrão é Common:: TimeSpan:: FromSeconds (30)|Dinâmico| Especifique TimeSpan em segundos. Isso representa o tempo limite de comunicação entre o host de aplicativo do usuário e o processo de malha para várias operações relacionadas à hospedagem, como o registro de fábrica; registro em tempo de execução. |
|RunAsPolicyEnabled| bool, o padrão é FALSE|Estático| Habilita a execução de pacotes de código como usuário local que não seja o usuário sob o qual o processo de malha está em execução. A fim de habilitar essa infraestrutura de política deve ser executada como sistema ou como usuário que tenha SeAssignPrimaryTokenPrivilege. |
|ServiceFactoryRegistrationTimeout| TimeSpan, o padrão é Common:: TimeSpan:: FromSeconds (120)|Dinâmico|Especifique TimeSpan em segundos. O valor de tempo limite para a chamada de infactory de registro de sincronização (com estado/estado) |
|ServiceTypeDisableFailureThreshold |Número inteiro, o padrão é 1 |Dinâmico|Esse é o limite para a contagem de falhas após o qual o Failovermanager (FM) é notificado para desabilitar o tipo de serviço nesse nó e tentar um nó diferente para posicionamento. |
|ServiceTypeDisableGraceInterval|TimeSpan, o padrão é Common:: TimeSpan:: FromSeconds (30)|Dinâmico|Especifique TimeSpan em segundos. Intervalo de tempo após o qual o tipo de serviço pode ser desabilitado |
|ServiceTypeRegistrationTimeout |Tempo em segundos, o padrão é 300 |Dinâmico|Tempo máximo permitido para o ServiceType ser registrado com a malha |
|UseContainerServiceArguments|bool, o padrão é TRUE|Estático|Essa configuração informa ao Hosting para ignorar os argumentos de passagem (especificados em config ContainerServiceArguments) para o daemon do Docker.|

## <a name="httpgateway"></a>HttpGateway

| **Parâmetro** | **Valores permitidos** | **Atualizar política** | **Orientação ou descrição resumida** |
| --- | --- | --- | --- |
|ActiveListeners |Uint, o padrão é 50 |Estático| Número de leituras a serem postadas na fila do servidor http. Isso controla o número de solicitações simultâneas que podem ser atendidas pelo HttpGateway. |
|HttpGatewayHealthReportSendInterval |Tempo em segundos, o padrão é 30 |Estático|Especifique TimeSpan em segundos. O intervalo no qual o gateway http envia relatórios de integridade acumulados para o Gerenciador de integridade. |
|HttpStrictTransportSecurityHeader|Cadeia de caracteres, o padrão é ""|Dinâmico| Especifique o valor do cabeçalho de segurança de transporte HTTP estrito a ser incluído em cada resposta enviada pelo HttpGateway. Quando definido como uma cadeia de caracteres vazia; Esse cabeçalho não será incluído na resposta do gateway.|
|IsEnabled|Bool, o padrão é false |Estático| Habilita/desabilita o HttpGateway. O HttpGateway está desabilitado por padrão. |
|MaxEntityBodySize |Uint, o padrão é 4194304 |Dinâmico|Fornece o tamanho máximo do corpo que pode ser esperado de uma solicitação HTTP. O valor padrão é 4 MB. O Httpgateway falhará em uma solicitação se tiver um corpo de tamanho > esse valor. O tamanho mínimo da parte de leitura é 4096 bytes. Isso deve ser > = 4096. |

## <a name="imagestoreservice"></a>ImageStoreService

| **Parâmetro** | **Valores permitidos** | **Atualizar política** | **Orientação ou descrição resumida** |
| --- | --- | --- | --- |
|Ativado |Bool, o padrão é false |Estático|O sinalizador habilitado para ImageStoreService. Padrão: false |
|MinReplicaSetSize | Int, o padrão é 3 |Estático|O MinReplicaSetSize para ImageStoreService. |
|PlacementConstraints | Cadeia de caracteres, o padrão é "" |Estático| O PlacementConstraints para ImageStoreService. |
|QuorumLossWaitDuration | Tempo em segundos, o padrão é MaxValue |Estático| Especifique TimeSpan em segundos. O QuorumLossWaitDuration para ImageStoreService. |
|ReplicaRestartWaitDuration | Tempo em segundos, o padrão é 60,0 \* 30 |Estático|Especifique TimeSpan em segundos. O ReplicaRestartWaitDuration para ImageStoreService. |
|StandByReplicaKeepDuration | Tempo em segundos, o padrão é 3600,0 \* 2 |Estático| Especifique TimeSpan em segundos. O StandByReplicaKeepDuration para ImageStoreService. |
|TargetReplicaSetSize | Int, o padrão é 7 |Estático|O TargetReplicaSetSize para ImageStoreService. |

## <a name="ktllogger"></a>KtlLogger

| **Parâmetro** | **Valores permitidos** | **Atualizar política** | **Orientação ou descrição resumida** |
| --- | --- | --- | --- |
|AutomaticMemoryConfiguration |Int, o padrão é 1 |Dinâmico|Sinalizador que indica se as configurações de memória devem ser configuradas de forma automática e dinâmica. Se for zero, as definições de configuração de memória serão usadas diretamente e não serão alteradas com base nas condições do sistema. Se houver, as configurações de memória serão configuradas automaticamente e poderão ser alteradas com base nas condições do sistema. |
|MaximumDestagingWriteOutstandingInKB | Int, o padrão é 0 |Dinâmico|O número de KB para permitir que o log compartilhado avance antecipadamente do log dedicado. Use 0 para indicar que não há limite.
|SharedLogId |Cadeia de caracteres, o padrão é "" |Estático|GUID exclusivo para o contêiner de log compartilhado. Use "" se estiver usando o caminho padrão na raiz de dados de malha. |
|SharedLogPath |Cadeia de caracteres, o padrão é "" |Estático|Caminho e nome do arquivo para o local para colocar o contêiner de log compartilhado. Use "" para usar o caminho padrão na raiz de dados de malha. |
|SharedLogSizeInMB |Int, o padrão é 8192 |Estático|O número de MB a serem alocados no contêiner de log compartilhado. |
|SharedLogThrottleLimitInPercentUsed|int, o padrão é 0 | Estático | A porcentagem de uso do log compartilhado que induzirá a limitação. O valor deve estar entre 0 e 100. Um valor de 0 implica o uso do valor percentual padrão. Um valor de 100 não implica nenhuma limitação. Um valor entre 1 e 99 especifica a porcentagem de uso de log acima da qual ocorrerá a limitação; por exemplo, se o log compartilhado for 10 GB e o valor for 90, a limitação ocorrerá quando 9GB estiver em uso. O uso do valor padrão é recomendado.|
|WriteBufferMemoryPoolMaximumInKB | Int, o padrão é 0 |Dinâmico|O número de KB para permitir que o pool de memória do buffer de gravação cresça até. Use 0 para indicar que não há limite. |
|WriteBufferMemoryPoolMinimumInKB |Int, o padrão é 8388608 |Dinâmico|O número de KB para alocar inicialmente para o pool de memória do buffer de gravação. Use 0 para indicar que nenhum padrão de limite deve ser consistente com SharedLogSizeInMB abaixo. |

## <a name="managedidentitytokenservice"></a>ManagedIdentityTokenService
| **Parâmetro** | **Valores permitidos** | **Atualizar política** | **Orientação ou descrição resumida** |
| --- | --- | --- | --- |
|IsEnabled|bool, o padrão é FALSE|Estático|Sinalizador que controla a presença e o status do serviço de token de identidade gerenciado no cluster; esse é um pré-requisito para usar a funcionalidade de identidade gerenciada de aplicativos Service Fabric.|

## <a name="management"></a>Gestão

| **Parâmetro** | **Valores permitidos** | **Atualizar política** | **Orientação ou descrição resumida** |
| --- | --- | --- | --- |
|AutomaticUnprovisionInterval|TimeSpan, o padrão é Common:: TimeSpan:: FromMinutes (5)|Dinâmico|Especifique TimeSpan em segundos. O intervalo de limpeza permitido para cancelar o registro do tipo de aplicativo durante a limpeza automática do tipo de aplicativo.|
|AzureStorageMaxConnections | Int, o padrão é 5000 |Dinâmico|O número máximo de conexões simultâneas com o armazenamento do Azure. |
|AzureStorageMaxWorkerThreads | Int, o padrão é 25 |Dinâmico|O número máximo de threads de trabalho em paralelo. |
|AzureStorageOperationTimeout | Tempo em segundos, o padrão é 6000 |Dinâmico|Especifique TimeSpan em segundos. Tempo limite para a operação XStore ser concluída. |
|CleanupApplicationPackageOnProvisionSuccess|bool, o padrão é FALSE |Dinâmico|Habilita ou desabilita a limpeza automática do pacote de aplicativos no provisionamento bem-sucedido. |
|CleanupUnusedApplicationTypes|Bool, o padrão é FALSE |Dinâmico|Essa configuração, se habilitada, permite cancelar automaticamente o registro de versões de tipo de aplicativo não utilizadas, ignorando as três versões não utilizadas mais recentes, reduzindo assim o espaço em disco ocupado pelo repositório de imagens. A limpeza automática será disparada no final do provisionamento bem-sucedido para esse tipo de aplicativo específico e também será executada periodicamente uma vez por dia para todos os tipos de aplicativos. O número de versões não utilizadas a serem ignoradas é configurável usando o parâmetro "MaxUnusedAppTypeVersionsToKeep". |
|DisableChecksumValidation | Bool, o padrão é false |Estático| Essa configuração nos permite habilitar ou desabilitar a validação da soma de verificação durante o provisionamento do aplicativo. |
|DisableServerSideCopy | Bool, o padrão é false |Estático|Essa configuração habilita ou desabilita a cópia do pacote de aplicativos no lado do servidor no ImageStore durante o provisionamento do aplicativo. |
|ImageCachingEnabled | Bool, o padrão é true |Estático|Essa configuração nos permite habilitar ou desabilitar o Caching. |
|ImageStoreConnectionString |SecureString |Estático|Cadeia de conexão para a raiz de ImageStore. |
|ImageStoreMinimumTransferBPS | Int, o padrão é 1024 |Dinâmico|A taxa de transferência mínima entre o cluster e o ImageStore. Esse valor é usado para determinar o tempo limite ao acessar o ImageStore externo. Altere esse valor somente se a latência entre o cluster e o ImageStore for alta para permitir mais tempo para o cluster baixar do ImageStore externo. |
|MaxUnusedAppTypeVersionsToKeep | Int, o padrão é 3 |Dinâmico|Essa configuração define o número de versões de tipo de aplicativo não usadas a serem ignoradas para limpeza. Esse parâmetro será aplicável somente se o parâmetro CleanupUnusedApplicationTypes estiver habilitado. |


## <a name="metricactivitythresholds"></a>MetricActivityThresholds
| **Parâmetro** | **Valores permitidos** |**Atualizar política**| **Orientação ou descrição resumida** |
| --- | --- | --- | --- |
|PropertyGroup|KeyIntegerValueMap, o padrão é None|Dinâmico|Determina o conjunto de MetricActivityThresholds para as métricas no cluster. O balanceamento funcionará se maxNodeLoad for maior que MetricActivityThresholds. Para métricas de desfragmentação, ele define a quantidade de carga igual ou abaixo da qual Service Fabric considerará o nó vazio |

## <a name="metricbalancingthresholds"></a>MetricBalancingThresholds
| **Parâmetro** | **Valores permitidos** |**Atualizar política**| **Orientação ou descrição resumida** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, o padrão é None|Dinâmico|Determina o conjunto de MetricBalancingThresholds para as métricas no cluster. O balanceamento funcionará se maxNodeLoad/minNodeLoad for maior que MetricBalancingThresholds. A desfragmentação funcionará se maxNodeLoad/minNodeLoad em pelo menos um FD ou UD for menor do que MetricBalancingThresholds. |

## <a name="metricloadstickinessforswap"></a>MetricLoadStickinessForSwap
| **Parâmetro** | **Valores permitidos** |**Atualizar política**| **Orientação ou descrição resumida** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, o padrão é None|Dinâmico|Determina a parte da carga que une com a réplica quando trocada leva um valor entre 0 (o carregamento não fica com a réplica) e 1 (carregar os módulos com réplica-padrão) |

## <a name="namingservice"></a>NamingService

| **Parâmetro** | **Valores permitidos** | **Atualizar política** | **Orientação ou descrição resumida** |
| --- | --- | --- | --- |
|GatewayServiceDescriptionCacheLimit |Int, o padrão é 0 |Estático|O número máximo de entradas mantidas no cache de descrição de serviço LRU no gateway de nomenclatura (definido como 0 para sem limite). |
|MaxClientConnections |Int, o padrão é 1000 |Dinâmico|O número máximo permitido de conexões de cliente por gateway. |
|MaxFileOperationTimeout |Tempo em segundos, o padrão é 30 |Dinâmico|Especifique TimeSpan em segundos. O tempo limite máximo permitido para a operação do serviço de repositório de arquivos. As solicitações que especificam um tempo limite maior serão rejeitadas. |
|MaxIndexedEmptyPartitions |Int, o padrão é 1000 |Dinâmico|O número máximo de partições vazias que permanecerão indexadas no cache de notificação para sincronizar clientes de reconexão. Todas as partições vazias acima desse número serão removidas do índice em ordem de versão de pesquisa crescente. A reconexão de clientes ainda pode sincronizar e receber atualizações de partição vazias perdidas; Mas o protocolo de sincronização torna-se mais caro. |
|MaxMessageSize |Int, o padrão é 4\*1024\*1024 |Estático|O tamanho máximo da mensagem para comunicação de nó de cliente ao usar o nome. Atenuação de ataque DOS; o valor padrão é 4 MB. |
|MaxNamingServiceHealthReports | Int, o padrão é 10 |Dinâmico|O número máximo de operações lentas que o serviço de repositório de nomeação relata como não íntegro de uma só vez. Se for 0; todas as operações lentas são enviadas. |
|MaxOperationTimeout |Tempo em segundos, o padrão é 600 |Dinâmico|Especifique TimeSpan em segundos. O tempo limite máximo permitido para operações do cliente. As solicitações que especificam um tempo limite maior serão rejeitadas. |
|MaxOutstandingNotificationsPerClient |Int, o padrão é 1000 |Dinâmico|O número máximo de notificações pendentes antes de um registro de cliente ser fechado forçosamente pelo Gateway. |
|MinReplicaSetSize | Int, o padrão é 3 |Não permitido| O número mínimo de réplicas de Serviço de Nomenclatura necessárias para gravar no para concluir uma atualização. Se houver menos réplicas do que esta ativa no sistema, o sistema de confiabilidade negará atualizações para o repositório de Serviço de Nomenclatura até que as réplicas sejam restauradas. Esse valor nunca deve ser maior do que o TargetReplicaSetSize. |
|PartitionCount |Int, o padrão é 3 |Não permitido|O número de partições do repositório de Serviço de Nomenclatura a ser criado. Cada partição possui uma única chave de partição que corresponde ao seu índice; Portanto, chaves de partição [0; PartitionCount] existe. Aumentar o número de partições de Serviço de Nomenclatura aumenta a escala que o Serviço de Nomenclatura pode executar ao diminuir a quantidade média de dados mantidos por qualquer conjunto de réplicas de backup; a um custo de maior utilização de recursos (desde que as réplicas de serviço PartitionCount * ReplicaSetSize precisem ser mantidas).|
|PlacementConstraints | Cadeia de caracteres, o padrão é "" |Não permitido| Restrição de posicionamento para o Serviço de Nomenclatura. |
|QuorumLossWaitDuration | Tempo em segundos, o padrão é MaxValue |Não permitido| Especifique TimeSpan em segundos. Quando um Serviço de Nomenclatura entra em perda de quorum; Esse temporizador é iniciado. Quando ele expirar, o FM considerará as réplicas como perdidas; e tentar recuperar o quorum. Não que isso possa resultar em perda de dados. |
|RepairInterval | Tempo em segundos, o padrão é 5 |Estático| Especifique TimeSpan em segundos. Intervalo no qual o reparo de inconsistência de nomenclatura entre o proprietário da autoridade e o proprietário do nome será iniciado. |
|ReplicaRestartWaitDuration | Tempo em segundos, o padrão é (60,0 * 30)|Não permitido| Especifique TimeSpan em segundos. Quando uma réplica de Serviço de Nomenclatura fica inativa; Esse temporizador é iniciado. Quando ele expirar, o FM começará a substituir as réplicas que estão inativas (ele ainda não as considera perdidas). |
|ServiceDescriptionCacheLimit | Int, o padrão é 0 |Estático| O número máximo de entradas mantidas no cache de descrição do serviço LRU no serviço de repositório de nomes (definido como 0 para sem limite). |
|ServiceNotificationTimeout |Tempo em segundos, o padrão é 30 |Dinâmico|Especifique TimeSpan em segundos. O tempo limite usado ao entregar notificações de serviço ao cliente. |
|StandByReplicaKeepDuration | Tempo em segundos, o padrão é 3600,0 * 2 |Não permitido| Especifique TimeSpan em segundos. Quando uma réplica de Serviço de Nomenclatura retorna de um estado inativo; Ele pode já ter sido substituído. Esse temporizador determina por quanto tempo o FM manterá a réplica em espera antes de descartar. |
|TargetReplicaSetSize |Int, o padrão é 7 |Não permitido|O número de conjuntos de réplicas para cada partição do repositório de Serviço de Nomenclatura. O aumento do número de conjuntos de réplicas aumenta o nível de confiabilidade das informações no repositório de Serviço de Nomenclatura; diminuindo a alteração de que as informações serão perdidas como resultado de falhas de nó; a um custo de maior carga em Windows Fabric e o tempo necessário para realizar atualizações nos dados de nomenclatura.|

## <a name="nodebufferpercentage"></a>NodeBufferPercentage
| **Parâmetro** | **Valores permitidos** |**Atualizar política**| **Orientação ou descrição resumida** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, o padrão é None|Dinâmico|Porcentagem de capacidade do nó por nome da métrica; usado como um buffer para manter algum lugar livre em um nó para o caso de failover. |

## <a name="nodecapacities"></a>NodeCapacities

| **Parâmetro** | **Valores permitidos** | **Atualizar política** | **Orientação ou descrição resumida** |
| --- | --- | --- | --- |
|PropertyGroup |NodeCapacityCollectionMap |Estático|Uma coleção de capacidades de nó para diferentes métricas. |

## <a name="nodedomainids"></a>NodeDomainIds

| **Parâmetro** | **Valores permitidos** | **Atualizar política** | **Orientação ou descrição resumida** |
| --- | --- | --- | --- |
|PropertyGroup |NodeFaultDomainIdCollection |Estático|Descreve os domínios de falha aos quais um nó pertence. O domínio de falha é definido por meio de um URI que descreve o local do nó no datacenter.  Os URIs de domínio de falha são do formato FD:/FD/seguidos por um segmento de caminho de URI.|
|UpgradeDomainId |Cadeia de caracteres, o padrão é "" |Estático|Descreve o domínio de atualização ao qual um nó pertence. |

## <a name="nodeproperties"></a>NodeProperties

| **Parâmetro** | **Valores permitidos** | **Atualizar política** | **Orientação ou descrição resumida** |
| --- | --- | --- | --- |
|PropertyGroup |NodePropertyCollectionMap |Estático|Uma coleção de pares chave-valor de cadeia de caracteres para propriedades de nó. |

## <a name="paas"></a>PaaS

| **Parâmetro** | **Valores permitidos** | **Atualizar política** | **Orientação ou descrição resumida** |
| --- | --- | --- | --- |
|ClusterId |Cadeia de caracteres, o padrão é "" |Não permitido|Repositório de certificados X509 usado pela malha para proteção de configuração. |

## <a name="performancecounterlocalstore"></a>PerformanceCounterLocalStore

| **Parâmetro** | **Valores permitidos** | **Atualizar política** | **Orientação ou descrição resumida** |
| --- | --- | --- | --- |
|Contadores |String | Dinâmico |Lista separada por vírgulas de contadores de desempenho a serem coletados. |
|IsEnabled |Bool, o padrão é true | Dinâmico |Sinalizador indica se a coleta do contador de desempenho no nó local está habilitada. |
|MaxCounterBinaryFileSizeInMB |Int, o padrão é 1 | Dinâmico |Tamanho máximo (em MB) para cada arquivo binário do contador de desempenho. |
|NewCounterBinaryFileCreationIntervalInMinutes |Int, o padrão é 10 | Dinâmico |Intervalo máximo (em segundos) após o qual um novo arquivo binário do contador de desempenho é criado. |
|SamplingIntervalInSeconds |Int, o padrão é 60 | Dinâmico |Intervalo de amostragem para os contadores de desempenho que estão sendo coletados. |

## <a name="placementandloadbalancing"></a>PlacementAndLoadBalancing

| **Parâmetro** | **Valores permitidos** | **Atualizar política** | **Orientação ou descrição resumida** |
| --- | --- | --- | --- |
|AffinityConstraintPriority | Int, o padrão é 0 | Dinâmico|Determina a prioridade da restrição de afinidade: 0: difícil; 1: Soft; negativo: ignorar. |
|ApplicationCapacityConstraintPriority | Int, o padrão é 0 | Dinâmico|Determina a prioridade da restrição de capacidade: 0: difícil; 1: Soft; negativo: ignorar. |
|AutoDetectAvailableResources|bool, o padrão é TRUE|Estático|Essa configuração irá disparar a detecção automática de recursos disponíveis no nó (CPU e memória) quando essa configuração for definida como true – vamos ler as capacidades reais e corrigi-las se o usuário especificou capacidades de nó inválidas ou não as definir se essa configuração estiver definida como false.  rastrear um aviso de que o usuário especificou capacidades de nó inválidos; Mas não os corrigiremos; o que significa que o usuário deseja ter as capacidades especificadas como > do que o nó realmente tem ou se as capacidades estão indefinidas; Ele assumirá capacidade ilimitada |
|BalancingDelayAfterNewNode | Tempo em segundos, o padrão é 120 |Dinâmico|Especifique TimeSpan em segundos. Não comece a balancear as atividades dentro desse período depois de adicionar um novo nó. |
|BalancingDelayAfterNodeDown | Tempo em segundos, o padrão é 120 |Dinâmico|Especifique TimeSpan em segundos. Não comece a balancear as atividades dentro desse período após um evento de nó inativo. |
|CapacityConstraintPriority | Int, o padrão é 0 | Dinâmico|Determina a prioridade da restrição de capacidade: 0: difícil; 1: Soft; negativo: ignorar. |
|ConsecutiveDroppedMovementsHealthReportLimit | Int, o padrão é 20 | Dinâmico|Define o número de vezes consecutivas que os movimentos ResourceBalancerdos são removidos antes que os diagnósticos sejam realizados e os avisos de integridade sejam emitidos. Negativo: nenhum aviso emitido sob essa condição. |
|ConstraintFixPartialDelayAfterNewNode | Tempo em segundos, o padrão é 120 |Dinâmico| Especifique TimeSpan em segundos. DDo não corrigir violações de restrição FaultDomain e UpgradeDomain nesse período depois de adicionar um novo nó. |
|ConstraintFixPartialDelayAfterNodeDown | Tempo em segundos, o padrão é 120 |Dinâmico| Especifique TimeSpan em segundos. Não corrija as violações de restrição FaultDomain e UpgradeDomain dentro desse período após um evento de nó inativo. |
|ConstraintViolationHealthReportLimit | Int, o padrão é 50 |Dinâmico| Define o número de vezes que a restrição violar a réplica deve ser persistentemente descorrigida antes que o diagnóstico seja conduzido e os relatórios de integridade sejam emitidos. |
|DetailedConstraintViolationHealthReportLimit | Int, o padrão é 200 |Dinâmico| Define o número de vezes que a restrição violar a réplica deve ser persistentemente descorrigida antes que os diagnósticos sejam conduzidos e relatórios de integridade detalhados sejam emitidos. |
|DetailedDiagnosticsInfoListLimit | Int, o padrão é 15 |Dinâmico| Define o número de entradas de diagnóstico (com informações detalhadas) por restrição a serem incluídas antes do truncamento no diagnóstico.|
|DetailedNodeListLimit | Int, o padrão é 15 |Dinâmico| Define o número de nós por restrição a serem incluídos antes do truncamento nos relatórios de réplica não posicionados. |
|DetailedPartitionListLimit | Int, o padrão é 15 |Dinâmico| Define o número de partições por entrada de diagnóstico para uma restrição a ser incluída antes do truncamento no diagnóstico. |
|DetailedVerboseHealthReportLimit | Int, o padrão é 200 | Dinâmico|Define o número de vezes que uma réplica não posicionada precisa ser persistentemente desalocada antes que os relatórios de integridade detalhados sejam emitidos. |
|EnforceUserServiceMetricCapacities|bool, o padrão é FALSE | Estático |Habilita a proteção dos serviços de malha. Todos os serviços de usuário estão em um objeto de trabalho/CGroup e limitados à quantidade especificada de recursos. Isso precisa ser estático (requer reinicialização de FabricHost) como criação/remoção de objeto de trabalho do usuário e limites de configuração em Done durante o Open do host de malha. |
|FaultDomainConstraintPriority | Int, o padrão é 0 |Dinâmico| Determina a prioridade da restrição de domínio de falha: 0: difícil; 1: Soft; negativo: ignorar. |
|GlobalMovementThrottleCountingInterval | Tempo em segundos, o padrão é 600 |Estático| Especifique TimeSpan em segundos. Indique o comprimento do intervalo passado para controlar os movimentos de réplica por domínio (usados junto com GlobalMovementThrottleThreshold). Pode ser definido como 0 para ignorar totalmente a limitação global. |
|GlobalMovementThrottleThreshold | Uint, o padrão é 1000 |Dinâmico| Número máximo de movimentos permitidos na fase de balanceamento no intervalo passado indicado por GlobalMovementThrottleCountingInterval. |
|GlobalMovementThrottleThresholdForBalancing | Uint, o padrão é 0 | Dinâmico|Número máximo de movimentações permitidas na fase de balanceamento no intervalo passado indicado por GlobalMovementThrottleCountingInterval. 0 indica nenhum limite. |
|GlobalMovementThrottleThresholdForPlacement | Uint, o padrão é 0 |Dinâmico| Número máximo de movimentos permitidos na fase de posicionamento no intervalo passado indicado por GlobalMovementThrottleCountingInterval. 0 indica que não há limite.|
|GlobalMovementThrottleThresholdPercentage|duplo, o padrão é 0|Dinâmico|Número máximo de movimentações totais permitidas nas fases de balanceamento e posicionamento (expresso como porcentagem do número total de réplicas no cluster) no intervalo passado indicado por GlobalMovementThrottleCountingInterval. 0 indica nenhum limite. Se isso e GlobalMovementThrottleThreshold forem especificados; em seguida, é usado um limite mais conservador.|
|GlobalMovementThrottleThresholdPercentageForBalancing|duplo, o padrão é 0|Dinâmico|Número máximo de movimentações permitidas na fase de balanceamento (expresso como porcentagem do número total de réplicas em PLB) no intervalo passado indicado por GlobalMovementThrottleCountingInterval. 0 indica nenhum limite. Se isso e GlobalMovementThrottleThresholdForBalancing forem especificados; em seguida, é usado um limite mais conservador.|
|InBuildThrottlingAssociatedMetric | Cadeia de caracteres, o padrão é "" |Estático| O nome da métrica associada para essa limitação. |
|InBuildThrottlingEnabled | Bool, o padrão é false |Dinâmico| Determine se a limitação no Build está habilitada. |
|InBuildThrottlingGlobalMaxValue | Int, o padrão é 0 |Dinâmico|O número máximo de réplicas em compilação permitidas globalmente. |
|InterruptBalancingForAllFailoverUnitUpdates | Bool, o padrão é false | Dinâmico|Determina se qualquer tipo de atualização de unidade de failover deve interromper a execução de balanceamento rápido ou lento. Com a execução de balanceamento "false" especificada será interrompida se FailoverUnit: for criado/excluído; tem réplicas ausentes; local da réplica primária alterado ou número alterado de réplicas. A execução de balanceamento não será interrompida em outros casos – se FailoverUnit: tiver réplicas extras; sinalizador de réplica alterado; alterada somente a versão de partição ou qualquer outro caso. |
|MinConstraintCheckInterval | Tempo em segundos, o padrão é 1 |Dinâmico| Especifique TimeSpan em segundos. Define a quantidade mínima de tempo que deve passar antes de duas rodas de verificação de restrição consecutivas. |
|MinLoadBalancingInterval | Tempo em segundos, o padrão é 5 |Dinâmico| Especifique TimeSpan em segundos. Define a quantidade mínima de tempo que deve passar antes de dois arredondamentos de balanceamento consecutivos. |
|MinPlacementInterval | Tempo em segundos, o padrão é 1 |Dinâmico| Especifique TimeSpan em segundos. Define a quantidade mínima de tempo que deve passar antes de duas rodas de posicionamento consecutivas. |
|MoveExistingReplicaForPlacement | Bool, o padrão é true |Dinâmico|Configuração que determina se é possível mover a réplica existente durante o posicionamento. |
|MovementPerPartitionThrottleCountingInterval | Tempo em segundos, o padrão é 600 |Estático| Especifique TimeSpan em segundos. Indica o comprimento do intervalo passado para o qual acompanhar os movimentos de réplica para cada partição (usada junto com MovementPerPartitionThrottleThreshold). |
|MovementPerPartitionThrottleThreshold | Uint, o padrão é 50 |Dinâmico| Nenhum movimento relacionado ao balanceamento ocorrerá para uma partição se o número de movimentos relacionados ao balanceamento para réplicas dessa partição tiver atingido ou excedido MovementPerFailoverUnitThrottleThreshold no intervalo passado indicado por MovementPerPartitionThrottleCountingInterval. |
|MoveParentToFixAffinityViolation | Bool, o padrão é false |Dinâmico| Configuração que determina se as réplicas pai podem ser movidas para corrigir as restrições de afinidade.|
|PartiallyPlaceServices | Bool, o padrão é true |Dinâmico| Determina se todas as réplicas de serviço no cluster serão colocadas "tudo ou nada" dadas os nós adequados limitados para eles.|
|PlaceChildWithoutParent | Bool, o padrão é true | Dinâmico|Configuração que determina se a réplica de serviço filho pode ser colocada se nenhuma réplica pai estiver ativa. |
|PlacementConstraintPriority | Int, o padrão é 0 | Dinâmico|Determina a prioridade da restrição de posicionamento: 0: difícil; 1: Soft; negativo: ignorar. |
|PlacementConstraintValidationCacheSize | Int, o padrão é 10000 |Dinâmico| Limita o tamanho da tabela usada para validação rápida e cache de expressões de restrição de posicionamento. |
|PlacementSearchTimeout | Tempo em segundos, o padrão é 0,5 |Dinâmico| Especifique TimeSpan em segundos. Ao colocar serviços; Pesquise pelo menos esse tempo antes de retornar um resultado. |
|PLBRefreshGap | Tempo em segundos, o padrão é 1 |Dinâmico| Especifique TimeSpan em segundos. Define a quantidade mínima de tempo que deve passar antes de o PLB atualizar o estado novamente. |
|PreferredLocationConstraintPriority | Int, o padrão é 2| Dinâmico|Determina a prioridade da restrição de local preferencial: 0: difícil; 1: Soft; 2: otimização; negativo: ignorar |
|PreferUpgradedUDs|bool, o padrão é FALSE|Dinâmico|Ativa e desativa a lógica que prefere mover para o UDs já atualizado. A partir do it 7,0, o valor padrão desse parâmetro é alterado de TRUE para FALSE.|
|PreventTransientOvercommit | Bool, o padrão é false | Dinâmico|Determina se PLB conta imediatamente em recursos que serão liberados pelas movimentações iniciadas. Por padrão; PLB pode iniciar a movimentação e a movimentação no mesmo nó que pode criar uma sobreconfirmação transitória. Definir esse parâmetro como true impedirá que esses tipos de excesso de confirmações e de desfragmentação sob demanda (também conhecido como placementWithMove) sejam desabilitados. |
|ScaleoutCountConstraintPriority | Int, o padrão é 0 |Dinâmico| Determina a prioridade da restrição de contagem de scale out: 0: Hard; 1: Soft; negativo: ignorar. |
|SwapPrimaryThrottlingAssociatedMetric | Cadeia de caracteres, o padrão é ""|Estático| O nome da métrica associada para essa limitação. |
|SwapPrimaryThrottlingEnabled | Bool, o padrão é false|Dinâmico| Determine se a limitação de troca primária está habilitada. |
|SwapPrimaryThrottlingGlobalMaxValue | Int, o padrão é 0 |Dinâmico| O número máximo de réplicas de troca primária permitidas globalmente. |
|TraceCRMReasons |Bool, o padrão é true |Dinâmico|Especifica se é para rastrear os motivos para os movimentos emitidos pelo CRM para o canal de eventos operacionais. |
|UpgradeDomainConstraintPriority | Int, o padrão é 1| Dinâmico|Determina a prioridade da restrição de domínio de atualização: 0: difícil; 1: Soft; negativo: ignorar. |
|UseMoveCostReports | Bool, o padrão é false | Dinâmico|Instrui o LB a ignorar o elemento de custo da função de Pontuação; resultando em um número potencialmente grande de movimentações para um posicionamento com balanceamento melhor. |
|UseSeparateSecondaryLoad | Bool, o padrão é true | Dinâmico|Configuração que determina se a carga secundária diferente é usada. |
|ValidatePlacementConstraint | Bool, o padrão é true |Dinâmico| Especifica se a expressão PlacementConstraint para um serviço é validada quando a ServiceDescription de um serviço é atualizada. |
|ValidatePrimaryPlacementConstraintOnPromote| Bool, o padrão é TRUE |Dinâmico|Especifica se a expressão PlacementConstraint para um serviço é avaliada para a preferência primária no failover. |
|VerboseHealthReportLimit | Int, o padrão é 20 | Dinâmico|Define o número de vezes que uma réplica deve ficar não alocada antes que um aviso de integridade seja relatado para ele (se o relatório de integridade detalhado estiver habilitado). |

## <a name="reconfigurationagent"></a>ReconfigurationAgent

| **Parâmetro** | **Valores permitidos** | **Atualizar política** | **Orientação ou descrição resumida** |
| --- | --- | --- | --- |
|ApplicationUpgradeMaxReplicaCloseDuration | Tempo em segundos, o padrão é 900 |Dinâmico|Especifique TimeSpan em segundos. A duração pela qual o sistema aguardará antes de encerrar os hosts de serviço que têm réplicas presas em fechamento durante a atualização do aplicativo.|
|FabricUpgradeMaxReplicaCloseDuration | Tempo em segundos, o padrão é 900 |Dinâmico| Especifique TimeSpan em segundos. A duração pela qual o sistema aguardará antes de encerrar os hosts de serviço que têm réplicas presas em fechamento durante a atualização da malha. |
|GracefulReplicaShutdownMaxDuration|TimeSpan, o padrão é Common:: TimeSpan:: FromSeconds (120)|Dinâmico|Especifique TimeSpan em segundos. A duração pela qual o sistema aguardará antes de encerrar os hosts de serviço que têm réplicas que estão presas no fechamento. Se esse valor for definido como 0, as réplicas não serão instruídas a fechar.|
|NodeDeactivationMaxReplicaCloseDuration | Tempo em segundos, o padrão é 900 |Dinâmico|Especifique TimeSpan em segundos. A duração pela qual o sistema aguardará antes de encerrar os hosts de serviço que têm réplicas presas em fechamento durante a desativação do nó. |
|PeriodicApiSlowTraceInterval | Tempo em segundos, o padrão é 5 minutos |Dinâmico| Especifique TimeSpan em segundos. PeriodicApiSlowTraceInterval define o intervalo em que as chamadas de API lentas serão rerastreadas pelo monitor de API. |
|ReplicaChangeRoleFailureRestartThreshold|int, o padrão é 10|Dinâmico| Inteiro. Especifique o número de falhas de API durante a promoção primária após a qual a ação de mitigação automática (reinicialização de réplica) será aplicada. |
|ReplicaChangeRoleFailureWarningReportThreshold|int, o padrão é 2147483647|Dinâmico| Inteiro. Especifique o número de falhas de API durante a promoção primária após o qual o relatório de integridade de aviso será gerado.|
|ServiceApiHealthDuration | Tempo em segundos, o padrão é 30 minutos |Dinâmico| Especifique TimeSpan em segundos. ServiceApiHealthDuration define por quanto tempo esperamos que uma API de serviço seja executada antes de relatar que ela não está íntegra. |
|ServiceReconfigurationApiHealthDuration | Tempo em segundos, o padrão é 30 |Dinâmico| Especifique TimeSpan em segundos. ServiceReconfigurationApiHealthDuration define por quanto tempo aguardamos que uma API de serviço seja executada antes de relatar não íntegro. Isso se aplica a chamadas de API que afetam a disponibilidade.|

## <a name="replication"></a>Replicação
| **Parâmetro** | **Valores permitidos** | **Atualizar política**| **Orientação ou descrição resumida** |
| --- | --- | --- | --- |
|BatchAcknowledgementInterval|TimeSpan, o padrão é Common:: TimeSpan:: FromMilliseconds (15)|Estático|Especifique TimeSpan em segundos. Determina a quantidade de tempo que o replicador aguarda depois de receber uma operação antes de enviar de volta uma confirmação. Outras operações recebidas durante esse período de tempo terão suas confirmações enviadas de volta em uma única mensagem-> reduzindo o tráfego de rede, mas potencialmente reduzindo a taxa de transferência do replicador.|
|MaxCopyQueueSize|uint, o padrão é 1024|Estático|Esse é o valor máximo que define o tamanho inicial para a fila que mantém as operações de replicação. Observe que ele deve ser uma potência de 2. Se, durante o tempo de execução, a fila crescer para essa operação de tamanho será limitada entre os replicadores primários e secundários.|
|MaxPrimaryReplicationQueueMemorySize|uint, o padrão é 0|Estático|Esse é o valor máximo da fila de replicação primária em bytes.|
|MaxPrimaryReplicationQueueSize|uint, o padrão é 1024|Estático|Este é o número máximo de operações que podem existir na fila de replicação primária. Observe que ele deve ser uma potência de 2.|
|MaxReplicationMessageSize|uint, o padrão é 52428800|Estático|Tamanho máximo da mensagem de operações de replicação. O padrão é 50 MB.|
|MaxSecondaryReplicationQueueMemorySize|uint, o padrão é 0|Estático|Esse é o valor máximo da fila de replicação secundária em bytes.|
|MaxSecondaryReplicationQueueSize|uint, o padrão é 2048|Estático|Este é o número máximo de operações que podem existir na fila de replicação secundária. Observe que ele deve ser uma potência de 2.|
|QueueHealthMonitoringInterval|TimeSpan, o padrão é Common:: TimeSpan:: FromSeconds (30)|Estático|Especifique TimeSpan em segundos. Esse valor determina o período de tempo usado pelo replicador para monitorar qualquer evento de integridade de aviso/erro nas filas de operação de replicação. Um valor de ' 0 ' desabilita o monitoramento de integridade |
|QueueHealthWarningAtUsagePercent|uint, o padrão é 80|Estático|Esse valor determina o uso da fila de replicação (em porcentagem) após o qual relatamos um aviso sobre o alto uso da fila. Fazemos isso após um intervalo de carência de QueueHealthMonitoringInterval. Se o uso da fila cair abaixo dessa porcentagem no intervalo de carência|
|ReplicatorAddress|Cadeia de caracteres, o padrão é "localhost: 0"|Estático|O ponto de extremidade na forma de uma cadeia de caracteres-' IP: Port ', que é usada pelo Windows Fabric Replicator para estabelecer conexões com outras réplicas a fim de enviar/receber operações.|
|ReplicatorListenAddress|Cadeia de caracteres, o padrão é "localhost: 0"|Estático|O ponto de extremidade na forma de uma cadeia de caracteres-' IP: Port ', que é usada pelo replicador Windows Fabric para receber operações de outras réplicas.|
|ReplicatorPublishAddress|Cadeia de caracteres, o padrão é "localhost: 0"|Estático|O ponto de extremidade na forma de uma cadeia de caracteres-' IP: Port ', que é usada pelo replicador Windows Fabric para enviar operações para outras réplicas.|
|RetryInterval|TimeSpan, o padrão é Common:: TimeSpan:: FromSeconds (5)|Estático|Especifique TimeSpan em segundos. Quando uma operação é perdida ou rejeitada, esse temporizador determina com que frequência o replicador tentará enviar a operação novamente.|

## <a name="resourcemonitorservice"></a>ResourceMonitorService
| **Parâmetro** | **Valores permitidos** | **Atualizar política**| **Orientação ou descrição resumida** |
| --- | --- | --- | --- |
|IsEnabled|bool, o padrão é FALSE |Estático|Controla se o serviço está habilitado no cluster ou não. |

## <a name="runas"></a>RunAs

| **Parâmetro** | **Valores permitidos** | **Atualizar política** | **Orientação ou descrição resumida** |
| --- | --- | --- | --- |
|RunAsAccountName |Cadeia de caracteres, o padrão é "" |Dinâmico|Indica o nome da conta Executar como. Isso só é necessário para o tipo de conta "DomainUser" ou "ManagedServiceAccount". Os valores válidos são "domínio \ usuário" ou "user@domain". |
|RunAsAccountType|Cadeia de caracteres, o padrão é "" |Dinâmico|Indica o tipo de conta Executar como. Isso é necessário para qualquer seção RunAs, os valores válidos são "DomainUser/NetworkService/ManagedServiceAccount/LocalSystem".|
|RunAsPassword|Cadeia de caracteres, o padrão é "" |Dinâmico|Indica a senha da conta RunAs. Isso só é necessário para o tipo de conta "DomainUser". |

## <a name="runas_dca"></a>RunAs_DCA

| **Parâmetro** | **Valores permitidos** | **Atualizar política** | **Orientação ou descrição resumida** |
| --- | --- | --- | --- |
|RunAsAccountName |Cadeia de caracteres, o padrão é "" |Dinâmico|Indica o nome da conta Executar como. Isso só é necessário para o tipo de conta "DomainUser" ou "ManagedServiceAccount". Os valores válidos são "domínio \ usuário" ou "user@domain". |
|RunAsAccountType|Cadeia de caracteres, o padrão é "" |Dinâmico|Indica o tipo de conta Executar como. Isso é necessário para qualquer seção RunAs, os valores válidos são "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunAsPassword|Cadeia de caracteres, o padrão é "" |Dinâmico|Indica a senha da conta RunAs. Isso só é necessário para o tipo de conta "DomainUser". |

## <a name="runas_fabric"></a>RunAs_Fabric

| **Parâmetro** | **Valores permitidos** | **Atualizar política** | **Orientação ou descrição resumida** |
| --- | --- | --- | --- |
|RunAsAccountName |Cadeia de caracteres, o padrão é "" |Dinâmico|Indica o nome da conta Executar como. Isso só é necessário para o tipo de conta "DomainUser" ou "ManagedServiceAccount". Os valores válidos são "domínio \ usuário" ou "user@domain". |
|RunAsAccountType|Cadeia de caracteres, o padrão é "" |Dinâmico|Indica o tipo de conta Executar como. Isso é necessário para qualquer seção RunAs, os valores válidos são "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunAsPassword|Cadeia de caracteres, o padrão é "" |Dinâmico|Indica a senha da conta RunAs. Isso só é necessário para o tipo de conta "DomainUser". |

## <a name="runas_httpgateway"></a>RunAs_HttpGateway

| **Parâmetro** | **Valores permitidos** | **Atualizar política** | **Orientação ou descrição resumida** |
| --- | --- | --- | --- |
|RunAsAccountName |Cadeia de caracteres, o padrão é "" |Dinâmico|Indica o nome da conta Executar como. Isso só é necessário para o tipo de conta "DomainUser" ou "ManagedServiceAccount". Os valores válidos são "domínio \ usuário" ou "user@domain". |
|RunAsAccountType|Cadeia de caracteres, o padrão é "" |Dinâmico|Indica o tipo de conta Executar como. Isso é necessário para qualquer seção RunAs, os valores válidos são "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunAsPassword|Cadeia de caracteres, o padrão é "" |Dinâmico|Indica a senha da conta RunAs. Isso só é necessário para o tipo de conta "DomainUser". |

## <a name="security"></a>Segurança
| **Parâmetro** | **Valores permitidos** |**Atualizar política**| **Orientação ou descrição resumida** |
| --- | --- | --- | --- |
|AADCertEndpointFormat|Cadeia de caracteres, o padrão é ""|Estático|Formato de ponto de extremidade de certificado do AAD, comercial padrão do Azure, especificado para um ambiente não padrão, como o Azure governamental "https:\//login.microsoftonline.us/{0}/FederationMetadata/2007-06/FederationMetadata.xml" |
|AADClientApplication|Cadeia de caracteres, o padrão é ""|Estático|Nome ou ID do aplicativo cliente nativo que representa clientes de malha |
|AADClusterApplication|Cadeia de caracteres, o padrão é ""|Estático|Nome ou ID do aplicativo da API Web que representa o cluster |
|AADLoginEndpoint|Cadeia de caracteres, o padrão é ""|Estático|Ponto de extremidade de logon do AAD, padrão do Azure comercial, especificado para um ambiente não padrão, como o Azure governamental "https:\//login.microsoftonline.us" |
|AADTenantId|Cadeia de caracteres, o padrão é ""|Estático|ID do locatário (GUID) |
|AcceptExpiredPinnedClusterCertificate|bool, o padrão é FALSE|Dinâmico|Sinalizador que indica se os certificados de cluster expirados declarados pela impressão digital só se aplicam a certificados de cluster; para manter o cluster em funcionamento. |
|AdminClientCertThumbprints|Cadeia de caracteres, o padrão é ""|Dinâmico|Impressões digitais de certificados usados por clientes na função de administrador. É uma lista de nomes separados por vírgulas. |
|AADTokenEndpointFormat|Cadeia de caracteres, o padrão é ""|Estático|Ponto de extremidade do token do AAD, padrão do Azure comercial, especificado para um ambiente não padrão, como o Azure governamental "https:\//login.microsoftonline.us//{0}" |
|AdminClientClaims|Cadeia de caracteres, o padrão é ""|Dinâmico|Todas as declarações possíveis esperadas de clientes administradores; o mesmo formato que ClientClaims; Essa lista é adicionada internamente ao ClientClaims; Portanto, não é necessário adicionar as mesmas entradas a ClientClaims. |
|AdminClientIdentities|Cadeia de caracteres, o padrão é ""|Dinâmico|Identidades do Windows de clientes de malha na função de administrador; usado para autorizar operações de malha privilegiada. É uma lista separada por vírgulas; cada entrada é um nome de conta de domínio ou nome de grupo. Para sua conveniência; a conta que executa o Fabric. exe é automaticamente atribuída à função de administrador; Portanto, é o grupo ServiceFabricAdministrators. |
|AppRunAsAccountGroupX509Folder|Cadeia de caracteres, o padrão é/Home/sfuser/sfusercerts |Estático|Pasta na qual os certificados AppRunAsAccountGroup X509 e as chaves privadas estão localizados |
|CertificateExpirySafetyMargin|TimeSpan, o padrão é Common:: TimeSpan:: FromMinutes (43200)|Estático|Especifique TimeSpan em segundos. Margem de segurança para expiração do certificado; o status do relatório de integridade do certificado muda de OK para aviso quando a expiração é mais próxima que isso. O padrão é 30 dias. |
|CertificateHealthReportingInterval|TimeSpan, o padrão é Common:: TimeSpan:: FromSeconds (3600 * 8)|Estático|Especifique TimeSpan em segundos. Especificar intervalo para relatório de integridade de certificado; o padrão é 8 horas; a configuração para 0 desabilita o relatório de integridade do certificado |
|ClientCertThumbprints|Cadeia de caracteres, o padrão é ""|Dinâmico|Impressões digitais de certificados usados pelos clientes para se comunicar com o cluster; o cluster usa esta autorizar conexão de entrada. É uma lista de nomes separados por vírgulas. |
|ClientClaimAuthEnabled|bool, o padrão é FALSE|Estático|Indica se a autenticação baseada em declarações está habilitada em clientes; a definição de true define ClientRoleEnabled implicitamente. |
|ClientClaims|Cadeia de caracteres, o padrão é ""|Dinâmico|Todas as declarações possíveis esperadas de clientes para conexão com o gateway. Esta é uma lista ' ou ': ClaimsEntry \|\| ClaimsEntry \|\| ClaimsEntry... cada ClaimsEntry é uma lista de "e": ClaimType = Declarevalue & & DeclareType = Claimvalue & & DeclareType = Declarevalue... |
|ClientIdentities|Cadeia de caracteres, o padrão é ""|Dinâmico|Identidades do Windows de FabricClient; o gateway de nomenclatura usa isso para autorizar conexões de entrada. É uma lista separada por vírgulas; cada entrada é um nome de conta de domínio ou nome de grupo. Para sua conveniência; a conta que executa o Fabric. exe é permitida automaticamente; Portanto, os grupos ServiceFabricAllowedUsers e ServiceFabricAdministrators. |
|ClientRoleEnabled|bool, o padrão é FALSE|Estático|Indica se a função de cliente está habilitada; Quando definido como true; Os clientes recebem funções com base em suas identidades. Para v2; habilitar isso significa que o cliente que não está no AdminClientCommonNames/AdminClientIdentities só pode executar operações somente leitura. |
|ClusterCertThumbprints|Cadeia de caracteres, o padrão é ""|Dinâmico|Impressões digitais de certificados com permissão para ingressar no cluster; uma lista de nomes separados por vírgulas. |
|ClusterCredentialType|Cadeia de caracteres, o padrão é "nenhum"|Não permitido|Indica o tipo de credenciais de segurança a ser usado para proteger o cluster. Os valores válidos são "None/X509/Windows" |
|ClusterIdentities|Cadeia de caracteres, o padrão é ""|Dinâmico|Identidades do Windows de nós de cluster; usado para autorização de associação de cluster. É uma lista separada por vírgulas; cada entrada é um nome de conta de domínio ou nome de grupo |
|ClusterSpn|Cadeia de caracteres, o padrão é ""|Não permitido|Nome da entidade de serviço do cluster; Quando a malha é executada como um usuário de domínio único (conta de usuário do gMSA/domínio). É o SPN de ouvintes de concessão e ouvintes em fabric. exe: ouvintes de Federação; ouvintes de replicação interna; ouvinte do serviço de runtime e ouvinte de gateway de nomenclatura. Isso deve ser deixado vazio quando a malha é executada como contas da máquina; Nesse caso, conectando o SPN do ouvinte de computação do endereço de transporte do ouvinte. |
|CrlCheckingFlag|uint, o padrão é 0x40000000|Dinâmico|Sinalizador de validação de cadeia de certificados padrão; pode ser substituído por um sinalizador específico do componente; por exemplo, Federation/X509CertChainFlags 0x10000000 CERT_CHAIN_REVOCATION_CHECK_END_CERT 0x20000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN 0x40000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT 0x80000000 CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY configuração como 0 desabilita a verificação de CRL a lista completa de valores com suporte é documentada por dwFlags de CertGetCertificateChain: https://msdn.microsoft.com/library/windows/desktop/aa376078(v=vs.85).aspx |
|CrlDisablePeriod|TimeSpan, o padrão é Common:: TimeSpan:: FromMinutes (15)|Dinâmico|Especifique TimeSpan em segundos. Por quanto tempo a verificação da CRL está desabilitada para um determinado certificado após encontrar um erro offline; Se o erro de CRL offline puder ser ignorado. |
|CrlOfflineHealthReportTtl|TimeSpan, o padrão é Common:: TimeSpan:: FromMinutes (1440)|Dinâmico|Especifique TimeSpan em segundos. |
|DisableFirewallRuleForDomainProfile| bool, o padrão é TRUE |Estático| Indica se a regra de firewall não deve ser habilitada para o perfil de domínio |
|DisableFirewallRuleForPrivateProfile| bool, o padrão é TRUE |Estático| Indica se a regra de firewall não deve ser habilitada para o perfil particular | 
|DisableFirewallRuleForPublicProfile| bool, o padrão é TRUE | Estático|Indica se a regra de firewall não deve ser habilitada para o perfil público |
| EnforceLinuxMinTlsVersion | bool, o padrão é FALSE | Dinâmico | Se definido como true; somente o TLS versão 1.2 + tem suporte.  Se falso; suporte a versões anteriores do TLS. Aplica-se somente ao Linux |
|FabricHostSpn| Cadeia de caracteres, o padrão é "" |Estático| Nome da entidade de serviço de FabricHost; Quando a malha é executada como um usuário de domínio único (conta de usuário do gMSA/domínio) e o FabricHost é executado na conta do computador. É o SPN do IPC listener para FabricHost; que, por padrão, deve ser deixado vazio, já que FabricHost é executado na conta do computador |
|IgnoreCrlOfflineError|bool, o padrão é FALSE|Dinâmico|Se deseja ignorar o erro de CRL offline quando o lado do servidor verifica certificados de cliente de entrada |
|IgnoreSvrCrlOfflineError|bool, o padrão é TRUE|Dinâmico|Se o erro de CRL offline deve ser ignorado quando o lado do cliente verifica os certificados de servidor de entrada; padrão como true. Os ataques com certificados de servidor revogados exigem um DNS de comprometimento; mais difícil do que com certificados de cliente revogados. |
|ServerAuthCredentialType|Cadeia de caracteres, o padrão é "nenhum"|Estático|Indica o tipo de credenciais de segurança a ser usado para proteger a comunicação entre o FabricClient e o cluster. Os valores válidos são "None/X509/Windows" |
|ServerCertThumbprints|Cadeia de caracteres, o padrão é ""|Dinâmico|Impressões digitais de certificados de servidor usados pelo cluster para se comunicar com os clientes; Os clientes usam isso para autenticar o cluster. É uma lista de nomes separados por vírgulas. |
|SettingsX509StoreName| Cadeia de caracteres, o padrão é "MY"| Dinâmico|Repositório de certificados X509 usado pela malha para proteção de configuração |
|UseClusterCertForIpcServerTlsSecurity|bool, o padrão é FALSE|Estático|Se o certificado de cluster deve ser usado para proteger a unidade de transporte TLS do IPC Server |
|X509Folder|Cadeia de caracteres, o padrão é/var/lib/waagent|Estático|Pasta em que os certificados X509 e as chaves privadas estão localizados |

## <a name="securityadminclientx509names"></a>Security/AdminClientX509Names

| **Parâmetro** | **Valores permitidos** | **Atualizar política** | **Orientação ou descrição resumida** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, o padrão é None|Dinâmico|Esta é uma lista de pares de "nome" e "valor". Cada "nome" é do nome comum da entidade ou DnsName de certificados X509 autorizados para operações do cliente de administrador. Para um determinado "nome", "valor" é uma lista separada por vírgulas de impressões digitais de certificado para fixação do emissor, se não estiver vazia, o emissor direto dos certificados de cliente do administrador deve estar na lista. |

## <a name="securityclientaccess"></a>Segurança/ClientAccess

| **Parâmetro** | **Valores permitidos** | **Atualizar política** | **Orientação ou descrição resumida** |
| --- | --- | --- | --- |
|ActivateNode |Cadeia de caracteres, o padrão é "admin" |Dinâmico| Configuração de segurança para ativação de um nó. |
|CancelTestCommand |Cadeia de caracteres, o padrão é "admin" |Dinâmico| Cancela um TestCommand específico-se ele estiver em trânsito. |
|CodePackageControl |Cadeia de caracteres, o padrão é "admin" |Dinâmico| Configuração de segurança para reiniciar pacotes de códigos. |
|CreateApplication |Cadeia de caracteres, o padrão é "admin" | Dinâmico|Configuração de segurança para a criação do aplicativo. |
|CreateComposeDeployment|Cadeia de caracteres, o padrão é "admin"| Dinâmico|Cria uma implantação de composição descrita por arquivos de composição |
|CreateGatewayResource|Cadeia de caracteres, o padrão é "admin"| Dinâmico|Criar um recurso de gateway |
|CreateName |Cadeia de caracteres, o padrão é "admin" |Dinâmico|Configuração de segurança para a criação de URI de nomenclatura. |
|Createnetwork|Cadeia de caracteres, o padrão é "admin" |Dinâmico|Cria uma rede de contêiner |
|CreateService |Cadeia de caracteres, o padrão é "admin" |Dinâmico| Configuração de segurança para a criação do serviço. |
|CreateServiceFromTemplate |Cadeia de caracteres, o padrão é "admin" |Dinâmico|Configuração de segurança para a criação de serviço a partir do modelo. |
|CreateVolume|Cadeia de caracteres, o padrão é "admin"|Dinâmico|Cria um volume |
|DeactivateNode |Cadeia de caracteres, o padrão é "admin" |Dinâmico| Configuração de segurança para desativar um nó. |
|DeactivateNodesBatch |Cadeia de caracteres, o padrão é "admin" |Dinâmico| Configuração de segurança para desativar vários nós. |
|Eliminar |Cadeia de caracteres, o padrão é "admin" |Dinâmico| Configurações de segurança para a operação de exclusão do cliente de repositório de imagens. |
|DeleteApplication |Cadeia de caracteres, o padrão é "admin" |Dinâmico| Configuração de segurança para exclusão de aplicativo. |
|DeleteComposeDeployment|Cadeia de caracteres, o padrão é "admin"| Dinâmico|Exclui a implantação de Compose |
|DeleteGatewayResource|Cadeia de caracteres, o padrão é "admin"| Dinâmico|Excluir um recurso de gateway |
|DeleteName |Cadeia de caracteres, o padrão é "admin" |Dinâmico|Configuração de segurança para a exclusão do URI de nomenclatura. |
|DeleteNetwork|Cadeia de caracteres, o padrão é "admin" |Dinâmico|Exclui uma rede de contêiner |
|DeleteService |Cadeia de caracteres, o padrão é "admin" |Dinâmico|Configuração de segurança para exclusão de serviço. |
|DeleteVolume|Cadeia de caracteres, o padrão é "admin"|Dinâmico|Exclui um volume.| 
|Enumeraproperties |Cadeia de caracteres, o padrão é "admin\|\|usuário" | Dinâmico|Configuração de segurança para a enumeração da propriedade de nomenclatura. |
|EnumerateSubnames |Cadeia de caracteres, o padrão é "admin\|\|usuário" |Dinâmico| Configuração de segurança para enumeração de URI de nomenclatura. |
|FileContent |Cadeia de caracteres, o padrão é "admin" |Dinâmico| Configuração de segurança para transferência de arquivos do cliente de repositório de imagens (externo ao cluster). |
|FileDownload |Cadeia de caracteres, o padrão é "admin" |Dinâmico| Configuração de segurança para o início do download do arquivo do cliente de repositório de imagens (externo ao cluster). |
|FinishInfrastructureTask |Cadeia de caracteres, o padrão é "admin" |Dinâmico| Configuração de segurança para concluir tarefas de infraestrutura. |
|GetChaosReport | Cadeia de caracteres, o padrão é "admin\|\|usuário" |Dinâmico| Busca o status do caos em um determinado intervalo de tempo. |
|GetClusterConfiguration | Cadeia de caracteres, o padrão é "admin\|\|usuário" | Dinâmico|Induzi GetClusterConfiguration em uma partição. |
|GetClusterConfigurationUpgradeStatus | Cadeia de caracteres, o padrão é "admin\|\|usuário" |Dinâmico| Induzi GetClusterConfigurationUpgradeStatus em uma partição. |
|GetFabricUpgradeStatus |Cadeia de caracteres, o padrão é "admin\|\|usuário" |Dinâmico| Configuração de segurança para sondagem do status de atualização do cluster. |
|Getfolderize |Cadeia de caracteres, o padrão é "admin" |Dinâmico|Configuração de segurança para obter o tamanho da pasta de FileStoreService |
|GetNodeDeactivationStatus |Cadeia de caracteres, o padrão é "admin" |Dinâmico| Configuração de segurança para verificar o status de desativação. |
|GetNodeTransitionProgress | Cadeia de caracteres, o padrão é "admin\|\|usuário" |Dinâmico| Configuração de segurança para obter o progresso em um comando de transição de nó. |
|GetPartitionDataLossProgress | Cadeia de caracteres, o padrão é "admin\|\|usuário" | Dinâmico|Busca o progresso de uma chamada de API de perda de dados de invocação. |
|GetPartitionQuorumLossProgress | Cadeia de caracteres, o padrão é "admin\|\|usuário" |Dinâmico| Busca o progresso de uma chamada de API de perda de quorum de invocação. |
|GetPartitionRestartProgress | Cadeia de caracteres, o padrão é "admin\|\|usuário" |Dinâmico| Busca o progresso de uma chamada de API de partição de reinicialização. |
|GetSecrets|Cadeia de caracteres, o padrão é "admin"|Dinâmico|Obter valores secretos |
|GetServiceDescription |Cadeia de caracteres, o padrão é "admin\|\|usuário" |Dinâmico| Configuração de segurança para notificações de serviço de sondagem longa e descrições de serviço de leitura. |
|GetStagingLocation |Cadeia de caracteres, o padrão é "admin" |Dinâmico| Configuração de segurança para recuperação do local de preparo do cliente de repositório de imagens. |
|GetStoreLocation |Cadeia de caracteres, o padrão é "admin" |Dinâmico| Configuração de segurança para recuperação do local do repositório do cliente do repositório de imagens. |
|GetUpgradeOrchestrationServiceState|Cadeia de caracteres, o padrão é "admin"| Dinâmico|Induzi GetUpgradeOrchestrationServiceState em uma partição |
|GetUpgradesPendingApproval |Cadeia de caracteres, o padrão é "admin" |Dinâmico| Induzi GetUpgradesPendingApproval em uma partição. |
|GetUpgradeStatus |Cadeia de caracteres, o padrão é "admin\|\|usuário" |Dinâmico| Configuração de segurança para sondagem do status de atualização do aplicativo. |
|Internolist |Cadeia de caracteres, o padrão é "admin" | Dinâmico|Configuração de segurança para operação de lista de arquivos do cliente de repositório de imagens (interna). |
|InvokeContainerApi|Cadeia de caracteres, o padrão é "admin"|Dinâmico|Invocar API de contêiner |
|InvokeInfrastructureCommand |Cadeia de caracteres, o padrão é "admin" |Dinâmico| Configuração de segurança para comandos de gerenciamento de tarefas de infraestrutura. |
|InvokeInfrastructureQuery |Cadeia de caracteres, o padrão é "admin\|\|usuário" | Dinâmico|Configuração de segurança para consultar tarefas de infraestrutura. |
|Lista |Cadeia de caracteres, o padrão é "admin\|\|usuário" | Dinâmico|Configuração de segurança para operação de lista de arquivos do cliente de repositório de imagens. |
|MoveNextFabricUpgradeDomain |Cadeia de caracteres, o padrão é "admin" |Dinâmico| Configuração de segurança para retomar atualizações de cluster com um domínio de atualização explícito. |
|MoveNextUpgradeDomain |Cadeia de caracteres, o padrão é "admin" |Dinâmico| Configuração de segurança para retomar atualizações de aplicativo com um domínio de atualização explícito. |
|MoveReplicaControl |Cadeia de caracteres, o padrão é "admin" | Dinâmico|Mover réplica. |
|NameExists |Cadeia de caracteres, o padrão é "admin\|\|usuário" | Dinâmico|Configuração de segurança para verificações de existência de URI de nomenclatura. |
|NodeControl |Cadeia de caracteres, o padrão é "admin" |Dinâmico| Configuração de segurança para iniciar; impedir e reiniciando nós. |
|NodeStateRemoved |Cadeia de caracteres, o padrão é "admin" |Dinâmico| Configuração de segurança para relatar o estado do nó removido. |
|Ping |Cadeia de caracteres, o padrão é "admin\|\|usuário" |Dinâmico| Configuração de segurança para pings de cliente. |
|PredeployPackageToNode |Cadeia de caracteres, o padrão é "admin" |Dinâmico| API de pré-implantação. |
|PrefixResolveService |Cadeia de caracteres, o padrão é "admin\|\|usuário" |Dinâmico| Configuração de segurança para resolução de prefixo de serviço baseada em reclamações. |
|PropertyReadBatch |Cadeia de caracteres, o padrão é "admin\|\|usuário" |Dinâmico| Configuração de segurança para operações de leitura de propriedade de nomenclatura. |
|PropertyWriteBatch |Cadeia de caracteres, o padrão é "admin" |Dinâmico|Configurações de segurança para operações de gravação de propriedade de nomenclatura. |
|ProvisionApplicationType |Cadeia de caracteres, o padrão é "admin" |Dinâmico| Configuração de segurança para provisionamento de tipo de aplicativo. |
|ProvisionFabric |Cadeia de caracteres, o padrão é "admin" |Dinâmico| Configuração de segurança para MSI e/ou provisionamento de manifesto de cluster. |
|Consulta |Cadeia de caracteres, o padrão é "admin\|\|usuário" |Dinâmico| Configuração de segurança para consultas. |
|RecoverPartition |Cadeia de caracteres, o padrão é "admin" | Dinâmico|Configuração de segurança para recuperar uma partição. |
|RecoverPartitions |Cadeia de caracteres, o padrão é "admin" | Dinâmico|Configuração de segurança para recuperar partições. |
|RecoverServicePartitions |Cadeia de caracteres, o padrão é "admin" |Dinâmico| Configuração de segurança para recuperar partições de serviço. |
|RecoverSystemPartitions |Cadeia de caracteres, o padrão é "admin" |Dinâmico| Configuração de segurança para recuperar partições de serviço do sistema. |
|RemoveNodeDeactivations |Cadeia de caracteres, o padrão é "admin" |Dinâmico| Configuração de segurança para reverter a desativação em vários nós. |
|ReportFabricUpgradeHealth |Cadeia de caracteres, o padrão é "admin" |Dinâmico| Configuração de segurança para retomar atualizações de cluster com o andamento da atualização atual. |
|ReportFault |Cadeia de caracteres, o padrão é "admin" |Dinâmico| Configuração de segurança para relatar falha. |
|ReportHealth |Cadeia de caracteres, o padrão é "admin" |Dinâmico| Configuração de segurança para relatório de integridade. |
|ReportUpgradeHealth |Cadeia de caracteres, o padrão é "admin" |Dinâmico| Configuração de segurança para retomar atualizações de aplicativos com o andamento da atualização atual. |
|ResetPartitionLoad |Cadeia de caracteres, o padrão é "admin\|\|usuário" |Dinâmico| Configuração de segurança para redefinir carregamento para um failoverUnit. |
|ResolveNameOwner |Cadeia de caracteres, o padrão é "admin\|\|usuário" | Dinâmico|Configuração de segurança para resolver o proprietário do URI de nomenclatura. |
|ResolvePartition |Cadeia de caracteres, o padrão é "admin\|\|usuário" | Dinâmico|Configuração de segurança para resolver serviços do sistema. |
|ResolveService |Cadeia de caracteres, o padrão é "admin\|\|usuário" |Dinâmico| Configuração de segurança para resolução de serviço baseada em reclamações. |
|ResolveSystemService|Cadeia de caracteres, o padrão é "admin\|\|usuário"|Dinâmico| Configuração de segurança para resolver serviços do sistema |
|RollbackApplicationUpgrade |Cadeia de caracteres, o padrão é "admin" |Dinâmico| Configuração de segurança para reverter atualizações de aplicativo. |
|RollbackFabricUpgrade |Cadeia de caracteres, o padrão é "admin" |Dinâmico| Configuração de segurança para reverter atualizações de cluster. |
|Notificações de notificação |Cadeia de caracteres, o padrão é "admin\|\|usuário" |Dinâmico| Configuração de segurança para notificações de serviço baseadas em evento. |
|SetUpgradeOrchestrationServiceState|Cadeia de caracteres, o padrão é "admin"| Dinâmico|Induzi SetUpgradeOrchestrationServiceState em uma partição |
|StartApprovedUpgrades |Cadeia de caracteres, o padrão é "admin" |Dinâmico| Induzi StartApprovedUpgrades em uma partição. |
|StartChaos |Cadeia de caracteres, o padrão é "admin" |Dinâmico| Inicia o caos – se ainda não tiver iniciado. |
|StartClusterConfigurationUpgrade |Cadeia de caracteres, o padrão é "admin" |Dinâmico| Induzi StartClusterConfigurationUpgrade em uma partição. |
|StartInfrastructureTask |Cadeia de caracteres, o padrão é "admin" | Dinâmico|Configuração de segurança para iniciar tarefas de infraestrutura. |
|StartNodeTransition |Cadeia de caracteres, o padrão é "admin" |Dinâmico| Configuração de segurança para iniciar uma transição de nó. |
|StartPartitionDataLoss |Cadeia de caracteres, o padrão é "admin" |Dinâmico| Induzi a perda de dados em uma partição. |
|StartPartitionQuorumLoss |Cadeia de caracteres, o padrão é "admin" |Dinâmico| Induzi a perda de quorum em uma partição. |
|StartPartitionRestart |Cadeia de caracteres, o padrão é "admin" |Dinâmico| Reinicia simultaneamente algumas ou todas as réplicas de uma partição. |
|StopChaos |Cadeia de caracteres, o padrão é "admin" |Dinâmico| Para o caos – se ele tiver sido iniciado. |
|ToggleVerboseServicePlacementHealthReporting | Cadeia de caracteres, o padrão é "admin\|\|usuário" |Dinâmico| Configuração de segurança para alternar o detalhado de substituição detalhado. |
|UnprovisionApplicationType |Cadeia de caracteres, o padrão é "admin" |Dinâmico| Configuração de segurança para o desprovisionamento de tipo de aplicativo. |
|UnprovisionFabric |Cadeia de caracteres, o padrão é "admin" |Dinâmico| Configuração de segurança para MSI e/ou desprovisionamento do manifesto do cluster. |
|UnreliableTransportControl |Cadeia de caracteres, o padrão é "admin" |Dinâmico| Transporte não confiável para adicionar e remover comportamentos. |
|UpdateService |Cadeia de caracteres, o padrão é "admin" |Dinâmico|Configuração de segurança para atualizações de serviço. |
|UpgradeApplication |Cadeia de caracteres, o padrão é "admin" |Dinâmico| Configuração de segurança para iniciar ou interromper atualizações de aplicativo. |
|UpgradeComposeDeployment|Cadeia de caracteres, o padrão é "admin"| Dinâmico|Atualiza a implantação do Compose |
|UpgradeFabric |Cadeia de caracteres, o padrão é "admin" |Dinâmico| Configuração de segurança para iniciar atualizações de cluster. |
|Carregar |Cadeia de caracteres, o padrão é "admin" | Dinâmico|Configuração de segurança para operação de carregamento do cliente de repositório de imagens. |

## <a name="securityclientcertificateissuerstores"></a>Security/ClientCertificateIssuerStores

| **Parâmetro** | **Valores permitidos** | **Atualizar política** | **Orientação ou descrição resumida** |
| --- | --- | --- | --- |
|PropertyGroup|IssuerStoreKeyValueMap, o padrão é None |Dinâmico|Repositórios de certificados do emissor X509 para certificados de cliente; Nome = clientIssuerCN; Valor = lista de repositórios separados por vírgula |

## <a name="securityclientx509names"></a>Security/ClientX509Names

| **Parâmetro** | **Valores permitidos** | **Atualizar política** | **Orientação ou descrição resumida** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, o padrão é None|Dinâmico|Esta é uma lista de pares de "nome" e "valor". Cada "nome" é do nome comum da entidade ou DnsName de certificados X509 autorizados para operações do cliente. Para um determinado "nome", "valor" é uma lista separada por vírgulas de impressões digitais de certificado para fixação do emissor, se não estiver vazia, o emissor direto dos certificados do cliente deverá estar na lista.|

## <a name="securityclustercertificateissuerstores"></a>Segurança/ClusterCertificateIssuerStores

| **Parâmetro** | **Valores permitidos** | **Atualizar política** | **Orientação ou descrição resumida** |
| --- | --- | --- | --- |
|PropertyGroup|IssuerStoreKeyValueMap, o padrão é None |Dinâmico|Repositórios de certificados do emissor X509 para certificados de cluster; Nome = clusterIssuerCN; Valor = lista de repositórios separados por vírgula |

## <a name="securityclusterx509names"></a>Segurança/ClusterX509Names

| **Parâmetro** | **Valores permitidos** | **Atualizar política** | **Orientação ou descrição resumida** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, o padrão é None|Dinâmico|Esta é uma lista de pares de "nome" e "valor". Cada "nome" é do nome comum da entidade ou DnsName de certificados X509 autorizados para operações de cluster. Para um determinado "nome", "valor" é uma lista separada por vírgulas de impressões digitais de certificado para fixação do emissor, se não estiver vazia, o emissor direto dos certificados de cluster deverá estar na lista.|

## <a name="securityservercertificateissuerstores"></a>Segurança/ServerCertificateIssuerStores

| **Parâmetro** | **Valores permitidos** | **Atualizar política** | **Orientação ou descrição resumida** |
| --- | --- | --- | --- |
|PropertyGroup|IssuerStoreKeyValueMap, o padrão é None |Dinâmico|Repositórios de certificados do emissor X509 para certificados de servidor; Nome = serverIssuerCN; Valor = lista de repositórios separados por vírgula |

## <a name="securityserverx509names"></a>Segurança/ServerX509Names

| **Parâmetro** | **Valores permitidos** | **Atualizar política** | **Orientação ou descrição resumida** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, o padrão é None|Dinâmico|Esta é uma lista de pares de "nome" e "valor". Cada "nome" é do nome comum da entidade ou DnsName de certificados X509 autorizados para operações de servidor. Para um determinado "nome", "valor" é uma lista separada por vírgulas de impressões digitais de certificado para fixação do emissor, se não estiver vazia, o emissor direto dos certificados de servidor deve estar na lista.|

## <a name="setup"></a>Configuração

| **Parâmetro** | **Valores permitidos** | **Atualizar política** | **Orientação ou descrição resumida** |
| --- | --- | --- | --- |
|ContainerNetworkName|Cadeia de caracteres, o padrão é ""| Estático |O nome da rede a ser usada ao configurar uma rede de contêiner.|
|ContainerNetworkSetup|bool, o padrão é FALSE| Estático |Se uma rede de contêiner deve ser configurada.|
|FabricDataRoot |String | Não permitido |Service Fabric diretório raiz de dados. O padrão para o Azure é d:\svcfab |
|FabricLogRoot |String | Não permitido |Diretório raiz de log do Service Fabric. É aí que os logs e rastreamentos do it são colocados. |
|NodesToBeRemoved|Cadeia de caracteres, o padrão é ""| Dinâmico |Os nós que devem ser removidos como parte da atualização de configuração. (Somente para implantações autônomas)|
|ServiceRunAsAccountName |String | Não permitido |O nome da conta sob a qual executar o serviço de host de malha. |
|SkipContainerNetworkResetOnReboot|bool, o padrão é FALSE|NotAllowed|Se a redefinição da rede de contêiner deve ser ignorada na reinicialização.|
|SkipFirewallConfiguration |Bool, o padrão é false | Não permitido |Especifica se as configurações de firewall precisam ser definidas pelo sistema ou não. Isso se aplicará somente se você estiver usando o Firewall do Windows. Se você estiver usando firewalls de terceiros, deverá abrir as portas para o sistema e os aplicativos a serem usados |

## <a name="tokenvalidationservice"></a>TokenValidationService

| **Parâmetro** | **Valores permitidos** | **Atualizar política** | **Orientação ou descrição resumida** |
| --- | --- | --- | --- |
|Fornecedores |Cadeia de caracteres, o padrão é "DSTS" |Estático|Lista separada por vírgulas de provedores de validação de token para habilitar (os provedores válidos são: DSTS; AAD). No momento, apenas um único provedor pode ser habilitado a qualquer momento. |

## <a name="traceetw"></a>Rastreamento/ETW

| **Parâmetro** | **Valores permitidos** | **Atualizar política** | **Orientação ou descrição resumida** |
| --- | --- | --- | --- |
|Nível |Int, o padrão é 4 | Dinâmico |O nível do ETW de rastreamento pode ter os valores 1, 2, 3, 4. Para ter suporte, você deve manter o nível de rastreamento em 4 |

## <a name="transactionalreplicator"></a>TransactionalReplicator

| **Parâmetro** | **Valores permitidos** | **Atualizar política** | **Orientação ou descrição resumida** |
| --- | --- | --- | --- |
|BatchAcknowledgementInterval | Tempo em segundos, o padrão é 0, 15 | Estático | Especifique TimeSpan em segundos. Determina a quantidade de tempo que o replicador aguarda depois de receber uma operação antes de enviar de volta uma confirmação. Outras operações recebidas durante esse período de tempo terão suas confirmações enviadas de volta em uma única mensagem-> reduzindo o tráfego de rede, mas potencialmente reduzindo a taxa de transferência do replicador. |
|MaxCopyQueueSize |Uint, o padrão é 16384 | Estático |Esse é o valor máximo que define o tamanho inicial para a fila que mantém as operações de replicação. Observe que ele deve ser uma potência de 2. Se, durante o tempo de execução, a fila crescer para essa operação de tamanho será limitada entre os replicadores primários e secundários. |
|MaxPrimaryReplicationQueueMemorySize |Uint, o padrão é 0 | Estático |Esse é o valor máximo da fila de replicação primária em bytes. |
|MaxPrimaryReplicationQueueSize |Uint, o padrão é 8192 | Estático |Este é o número máximo de operações que podem existir na fila de replicação primária. Observe que ele deve ser uma potência de 2. |
|MaxReplicationMessageSize |Uint, o padrão é 52428800 | Estático | Tamanho máximo da mensagem de operações de replicação. O padrão é 50 MB. |
|MaxSecondaryReplicationQueueMemorySize |Uint, o padrão é 0 | Estático |Esse é o valor máximo da fila de replicação secundária em bytes. |
|MaxSecondaryReplicationQueueSize |Uint, o padrão é 16384 | Estático |Este é o número máximo de operações que podem existir na fila de replicação secundária. Observe que ele deve ser uma potência de 2. |
|ReplicatorAddress |Cadeia de caracteres, o padrão é "localhost: 0" | Estático | O ponto de extremidade na forma de uma cadeia de caracteres-' IP: Port ', que é usada pelo Windows Fabric Replicator para estabelecer conexões com outras réplicas a fim de enviar/receber operações. |

## <a name="transport"></a>Transporte
| **Parâmetro** | **Valores permitidos** |**Atualizar política** |**Orientação ou descrição resumida** |
| --- | --- | --- | --- |
|ConnectionOpenTimeout|TimeSpan, o padrão é Common:: TimeSpan:: FromSeconds (60)|Estático|Especifique TimeSpan em segundos. Tempo limite para a configuração de conexão no lado de entrada e de aceitação (incluindo a negociação de segurança no modo de segurança) |
|FrameHeaderErrorCheckingEnabled|bool, o padrão é TRUE|Estático|Configuração padrão para verificação de erro no cabeçalho do quadro no modo não seguro; a configuração do componente substitui isso. |
|MessageErrorCheckingEnabled|bool, o padrão é FALSE|Estático|Configuração padrão para verificação de erros no cabeçalho e no corpo da mensagem no modo não seguro; a configuração do componente substitui isso. |
|ResolveOption|Cadeia de caracteres, o padrão é "não especificado"|Estático|Determina como o FQDN é resolvido.  Os valores válidos são "não especificado/IPv4/IPv6". |
|SendTimeout|TimeSpan, o padrão é Common:: TimeSpan:: FromSeconds (300)|Dinâmico|Especifique TimeSpan em segundos. Tempo limite de envio para detecção de conexão travada. Os relatórios de falha de TCP não são confiáveis em algum ambiente. Isso pode precisar ser ajustado de acordo com a largura de banda de rede disponível e o tamanho dos dados de saída (\*MaxMessageSize\/\*SendQueueSizeLimit). |

## <a name="upgradeorchestrationservice"></a>UpgradeOrchestrationService

| **Parâmetro** | **Valores permitidos** | **Atualizar política** | **Orientação ou descrição resumida** |
| --- | --- | --- | --- |
|AutoupgradeEnabled | Bool, o padrão é true |Estático| Ação de sondagem e atualização automática com base em um arquivo de estado de meta. |
|AutoupgradeInstallEnabled|Bool, o padrão é FALSE|Estático|Sondagem automática, provisionamento e instalação de ação de atualização de código com base em um arquivo de estado de meta.|
|GoalStateExpirationReminderInDays|int, o padrão é 30|Estático|Define o número de dias restantes após o qual o lembrete de estado de meta deve ser mostrado.|
|MinReplicaSetSize |Int, o padrão é 0 |Estático |O MinReplicaSetSize para UpgradeOrchestrationService.|
|PlacementConstraints | Cadeia de caracteres, o padrão é "" |Estático| O PlacementConstraints para UpgradeOrchestrationService. |
|QuorumLossWaitDuration | Tempo em segundos, o padrão é MaxValue |Estático| Especifique TimeSpan em segundos. O QuorumLossWaitDuration para UpgradeOrchestrationService. |
|ReplicaRestartWaitDuration | Tempo em segundos, o padrão é 60 minutos|Estático| Especifique TimeSpan em segundos. O ReplicaRestartWaitDuration para UpgradeOrchestrationService. |
|StandByReplicaKeepDuration | Tempo em segundos, o padrão é 60*24*7 minutos |Estático| Especifique TimeSpan em segundos. O StandByReplicaKeepDuration para UpgradeOrchestrationService. |
|TargetReplicaSetSize |Int, o padrão é 0 |Estático |O TargetReplicaSetSize para UpgradeOrchestrationService. |
|UpgradeApprovalRequired | Bool, o padrão é false | Estático|Configuração para fazer a atualização de código exigir aprovação do administrador antes de continuar. |

## <a name="upgradeservice"></a>UpgradeService

| **Parâmetro** | **Valores permitidos** | **Atualizar política** | **Orientação ou descrição resumida** |
| --- | --- | --- | --- |
|BaseUrl | Cadeia de caracteres, o padrão é "" |Estático|BaseUrl para UpgradeService. |
|ClusterId | Cadeia de caracteres, o padrão é "" |Estático|Clusterid para UpgradeService. |
|Coordinator | Cadeia de caracteres, o padrão é "WUTest"|Não permitido|O Coordinator para UpgradeService. |
|MinReplicaSetSize | Int, o padrão é 2 |Não permitido| O MinReplicaSetSize para UpgradeService. |
|OnlyBaseUpgrade | Bool, o padrão é false |Dinâmico|OnlyBaseUpgrade para UpgradeService. |
|PlacementConstraints |Cadeia de caracteres, o padrão é "" |Não permitido|O PlacementConstraints para o serviço de atualização. |
|PollIntervalInSeconds|TimeSpan, o padrão é Common:: TimeSpan:: FromSeconds (60) |Dinâmico|Especifique TimeSpan em segundos. O intervalo entre o UpgradeService Poll para operações de gerenciamento do ARM. |
|TargetReplicaSetSize | Int, o padrão é 3 |Não permitido| O TargetReplicaSetSize para UpgradeService. |
|TestCabFolder | Cadeia de caracteres, o padrão é "" |Estático| TestCabFolder para UpgradeService. |
|X509FindType | Cadeia de caracteres, o padrão é ""|Dinâmico| X509FindType para UpgradeService. |
|X509FindValue | Cadeia de caracteres, o padrão é "" |Dinâmico| X509FindValue para UpgradeService. |
|X509SecondaryFindValue | Cadeia de caracteres, o padrão é "" |Dinâmico| X509SecondaryFindValue para UpgradeService. |
|X509StoreLocation | Cadeia de caracteres, o padrão é "" |Dinâmico| X509StoreLocation para UpgradeService. |
|X509StoreName | Cadeia de caracteres, o padrão é "My"|Dinâmico|X509StoreName para UpgradeService. |

## <a name="userservicemetriccapacities"></a>UserServiceMetricCapacities
| **Parâmetro** | **Valores permitidos** | **Atualizar política** | **Orientação ou descrição resumida** |
| --- | --- | --- | --- |
|PropertyGroup| UserServiceMetricCapacitiesMap, o padrão é None | Estático | Uma coleção de limites de governança de recursos dos serviços de usuário precisa ser estática, pois afeta a lógica de detecção automática |

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações, consulte [atualizar a configuração de um cluster do Azure](service-fabric-cluster-config-upgrade-azure.md) e [atualizar a configuração de um cluster autônomo](service-fabric-cluster-config-upgrade-windows-server.md).
