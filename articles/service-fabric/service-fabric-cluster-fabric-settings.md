---
title: Alterar definições de cluster de tecido de serviço Azure
description: Este artigo descreve as definições de tecido e as políticas de atualização de tecido que pode personalizar.
ms.topic: reference
ms.date: 08/30/2019
ms.openlocfilehash: 01f8eb861a1fc53ad95a95d7695df8e4b5b8a2ab
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78393278"
---
# <a name="customize-service-fabric-cluster-settings"></a>Personalizar as definições do cluster do tecido de serviço
Este artigo descreve as várias definições de tecido para o seu cluster De Tecido de Serviço que pode personalizar. Para clusters hospedados em Azure, pode personalizar as definições através do [portal Azure](https://portal.azure.com) ou utilizando um modelo de Gestor de Recursos Azure. Para mais informações, consulte [Atualizar a configuração de um cluster Azure](service-fabric-cluster-config-upgrade-azure.md). Para clusters autónomos, personaliza as definições atualizando o ficheiro *ClusterConfig.json* e realizando uma atualização de configuração no seu cluster. Para mais informações, consulte [Atualizar a configuração de um cluster autónomo](service-fabric-cluster-config-upgrade-windows-server.md).

Existem três políticas diferentes de upgrade:

- **Dinâmica** – Alterações a uma configuração dinâmica não causam qualquer reinício de processo suprime os processos do Tecido de Serviço ou os processos do anfitrião do serviço. 
- **Estática** – As alterações a uma configuração estática farão com que o nó de Tecido de Serviço reinicie para consumir a alteração. Os serviços nos nódosos serão reiniciados.
- **NotAllowed** – Estas definições não podem ser modificadas. A alteração destas definições requer que o cluster seja destruído e um novo cluster criado. 

Segue-se uma lista de definições de Tecido que pode personalizar, organizadapor secção.

## <a name="applicationgatewayhttp"></a>ApplicationGateway/Http

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|ApplicationCertificateValidationPolicy|cadeia, padrão é "Nenhum"|Estático| Isto não valida o certificado do servidor; suceder ao pedido. Consulte as impressões de config ServiceCertificateThumb para a lista separada da vírcula das impressões digitais dos certificados remotos em que o representante inverso pode confiar. Consulte o config ServiceCommonNameAndIssuer para obter o nome do assunto e impressão digital do emitente das certidões remotas em que o representante inverso pode confiar. Para saber mais, consulte a [ligação segura de procuração inversa](service-fabric-reverseproxy-configure-secure-communication.md#secure-connection-establishment-between-the-reverse-proxy-and-services). |
|BodyChunkSize |Uint, padrão é 16384 |Dinâmica| Dá o tamanho do pedaço em bytes usados para ler o corpo. |
|CrlCheckingFlag|uint, padrão é 0x40000000000 |Dinâmica| Bandeiras para validação da cadeia de certificados de pedido/serviço; por exemplo, a CRL a verificar 0x1000000000CERT_CHAIN_REVOCATION_CHECK_END_CERT 0x2000000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN 0x4000000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT 0x8000000000 CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY Definição para 0 desativar a verificação de CRL A lista completa dos valores suportados é documentada pelo dwFlags of CertGetCertificateChain: https://msdn.microsoft.com/library/windows/desktop/aa376078(v=vs.85).aspx  |
|DefaultHttpRequestTimeout |Tempo em segundos. padrão é 120 |Dinâmica|Especifique a hora de tempo em segundos.  Dá o tempo de pedido padrão para os pedidos de http que estão sendo processados no gateway da aplicação http. |
|ForwardClientCertificate|Bool, o padrão é FALSO|Dinâmica|Quando definido como falso, o proxy inverso não solicitará o certificado de cliente. Quando definido como verdadeiro, o proxy inverso solicitará o certificado de cliente durante o aperto de mão SSL e encaminhará a cadeia de formato PEM codificada base64 para o serviço num cabeçalho chamado X-Cliente-Certificado.O serviço pode falhar o pedido com o código de estado apropriado depois de inspecionar os dados do certificado. Se isso for verdade e o cliente não apresentar um certificado, o proxy inverso encaminhará um cabeçalho vazio e deixará o serviço tratar do caso. O procuramento inverso funcionará como uma camada transparente. Para saber mais, consulte [Configurar a autenticação](service-fabric-reverseproxy-configure-secure-communication.md#setting-up-client-certificate-authentication-through-the-reverse-proxy)do certificado de cliente. |
|GatewayAuthCredentialType |cadeia, padrão é "Nenhum" |Estático| Indica o tipo de credenciais de segurança a utilizar no ponto final da aplicação http valores válidos são None/X509. |
|GatewayX509CertificateFindType |cadeia, padrão é "FindByThumbprint" |Dinâmica| Indica como procurar um certificado na loja especificada pelo valor suportado gatewayX509CertificateStoreName: FindByThumbprint; FindBySubjectName. |
|GatewayX509CertificateFindValue | cadeia, padrão é "" |Dinâmica| Pesquisar valor de filtro usado para localizar o certificado de gateway da aplicação http. Este certificado está configurado no ponto final https e também pode ser usado para verificar a identidade da app se necessário pelos serviços. FindValue é procurado primeiro; e se isso não existir; FindValueSecondary é procurado. |
|GatewayX509CertificateFindValueSecondary | cadeia, padrão é "" |Dinâmica|Pesquisar valor de filtro usado para localizar o certificado de gateway da aplicação http. Este certificado está configurado no ponto final https e também pode ser usado para verificar a identidade da app se necessário pelos serviços. FindValue é procurado primeiro; e se isso não existir; FindValueSecondary é procurado.|
|GatewayX509CertificateStoreName |cadeia, padrão é "O meu" |Dinâmica| Nome da loja de certificados X.509 que contém certificado para gateway de aplicações http. |
|HttpRequestConnectTimeout|TimeSpan, o padrão é comum:TimeSpan::FromSeconds (5)|Dinâmica|Especifique a hora de tempo em segundos.  Dá o tempo de ligação para os pedidos de http enviados a partir do gateway da aplicação http.  |
|IgnoreCrlOfflineError|Bool, o padrão é VERDADE|Dinâmica|Se ignorar o erro offline da CRL para verificação de certificado de aplicação/serviço. |
|IsEnabled |Bool, o padrão é falso. |Estático| Ativa/desativa o HttpApplicationGateway. HttpApplicationGateway é desativado por padrão e este config precisa de ser definido para o ativar. |
|NumberOfParallelOperations | Uint, padrão é 5000 |Estático|Número de leituras para publicar na fila do servidor http. Isto controla o número de pedidos simultâneos que podem ser satisfeitos pelo HttpGateway. |
|RemoveServiceResponseHeaders|cadeia, padrão é "Data; Servidor"|Estático|Lista semi-cólon/vírgula separada de cabeçalhos de resposta que serão removidos da resposta do serviço; antes de encaminhar para o cliente. Se isto estiver definido para uma corda vazia; passar todos os cabeçalhos devolvidos pelo serviço como é. i.e não sobrepor a Data e o Servidor |
|ResolveServiceBackoffInterval |Tempo em segundos, padrão é 5 |Dinâmica|Especifique a hora de tempo em segundos.  Dá o intervalo de back-off padrão antes de tentar novamente uma operação de serviço de resolução falhada. |
|SecureOnlyMode|Bool, o padrão é FALSO|Dinâmica| SecureOnlyMode: verdadeiro: O Proxy inverso só irá encaminhar-se para os serviços que publicam pontos finais seguros. falso: O Proxy inverso pode encaminhar pedidos para pontos finais seguros/não seguros. Para saber mais, consulte a lógica de seleção de [pontos finais de procuração inversa.](service-fabric-reverseproxy-configure-secure-communication.md#endpoint-selection-logic-when-services-expose-secure-as-well-as-unsecured-endpoints)  |
|ServiceCertificateThumbprints|cadeia, padrão é ""|Dinâmica|A lista separada da vírcula das impressões digitais dos certificados remotos em que o representante inverso pode confiar. Para saber mais, consulte a [ligação segura de procuração inversa](service-fabric-reverseproxy-configure-secure-communication.md#secure-connection-establishment-between-the-reverse-proxy-and-services). |

## <a name="applicationgatewayhttpservicecommonnameandissuer"></a>ApplicationGateway/Http/ServiceCommonNameAndIssuer

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, padrão é Nenhum|Dinâmica| Nome do assunto e impressão digital emitentes das certidões remotas em que o representante inverso pode confiar. Para saber mais, consulte a [ligação segura de procuração inversa](service-fabric-reverseproxy-configure-secure-communication.md#secure-connection-establishment-between-the-reverse-proxy-and-services). |

## <a name="backuprestoreservice"></a>BackupRestoreService

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|MinReplicaSetSize|int, padrão é 0|Estático|O MinReplicaSetSize para BackupRestoreService |
|PlacementConstraints|cadeia, padrão é ""|Estático|  Os Constrangimentos de Colocação para o serviço BackupRestore |
|SecretEncryptionCertThumbprint|cadeia, padrão é ""|Dinâmica|Impressão digital do certificado de encriptação Secreta X509 |
|SecretEncryptionCertX509StoreName|cadeia, padrão é "O meu"|   Dinâmica|    Isto indica o certificado a utilizar para encriptação e desencriptação de creds Nome da loja de certificados X.509 que é usado para encriptar credenciais de loja desencriptação usadas pelo serviço Backup Restore |
|TargetReplicaSetSize|int, padrão é 0|Estático| O TargetReplicaSetSize para BackupRestoreService |

## <a name="clustermanager"></a>ClusterManager

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|PermitirPolíticas de UpgradeS | Bool, o padrão é falso. |Dinâmica|Se as políticas de classificação personalizada são ou não permitidas. Isto é utilizado para realizar uma atualização de 2 fases que permite esta funcionalidade. O Serviço Fabric 6.5 adiciona suporte para especificar a política de classificação para domínios de atualização durante atualizações de cluster ou aplicações. As políticas apoiadas são numéricas, lexicográficas, reveréricas e inversas Lexicográficas. O padrão é numérico. Para poder utilizar esta funcionalidade, o manifesto cluster definindo clusterManager/ AllowCustomUpgradeSortPolicies deve ser definido para True como uma segunda etapa de upgrade de config após o código SF 6.5 ter concluído a atualização. É importante que isto seja feito em duas fases, caso contrário a atualização do código pode ficar confusa sobre a ordem de atualização durante a primeira atualização.|
|EnableDefaultServicesUpgrade | Bool, o padrão é falso. |Dinâmica|Ativar a atualização dos serviços predefinidos durante a atualização da aplicação. As descrições do serviço por defeito seriam substituídas após a atualização. |
|FabricUpgradeHealthCheckInterval |Tempo em segundos, padrão é 60 |Dinâmica|A frequência da verificação do estado de saúde durante uma atualização de tecido monitorizado |
|FabricUpgradeStatusPollInterval |Tempo em segundos, padrão é 60 |Dinâmica|A frequência das sondagens para o estado de atualização do Tecido. Este valor determina a taxa de atualização para qualquer chamada GetFabricUpgradeProgress |
|ImageBuilderTimeoutBuffer |Tempo em segundos, padrão é 3 |Dinâmica|Especifique a hora de tempo em segundos. O tempo para permitir que erros específicos de timeout do Image Builder regressem ao cliente. Se este tampão for muito pequeno; em seguida, o cliente horário saem antes do servidor e obtém um erro de timeout genérico. |
|InfrastructureTaskHealthCheckRetryTimeout | Tempo em segundos, padrão é 60 |Dinâmica|Especifique a hora de tempo em segundos. O tempo para passar a tentar verificações de saúde falhadas enquanto pós-processamento uma tarefa de infraestrutura. Observar um exame de saúde aprovado vai repor este temporizador. |
|InfrastructureTaskHealthCheckStableDuration | Tempo em segundos, padrão é 0|Dinâmica| Especifique a hora de tempo em segundos. O tempo para observar controlos de saúde consecutivos passou antes do pós-processamento de uma tarefa de infraestrutura terminar com sucesso. Observar um exame de saúde falhado redefinirá este temporizador. |
|InfrastructureTaskHealthCheckWaitDuration |Tempo em segundos, padrão é 0|Dinâmica| Especifique a hora de tempo em segundos. O tempo de espera antes de iniciar os controlos de saúde após o pós-processamento de uma tarefa de infraestrutura. |
|InfrastructureTaskProcessingInterval | Tempo em segundos, padrão é 10 |Dinâmica|Especifique a hora de tempo em segundos. O intervalo de processamento utilizado pela máquina estatal de processamento de tarefas de infraestrutura. |
|MaxCommunicationTimeout |Tempo em segundos, padrão é 600 |Dinâmica|Especifique a hora de tempo em segundos. O prazo máximo para as comunicações internas entre clusterManager e outros serviços de sistema (isto é,; Serviço de Nomeação; Gestor de Failover e etc.). Este prazo deve ser menor do que o MaxOperationTimeout global (uma vez que pode haver múltiplas comunicações entre componentes do sistema para cada operação do cliente). |
|MaxDataMigrationTimeout |Tempo em segundos, padrão é 600 |Dinâmica|Especifique a hora de tempo em segundos. O prazo máximo para as operações de recuperação da migração de dados após a realização de uma atualização do Tecido. |
|MaxOperationRetryDelay |Tempo em segundos, padrão é 5|Dinâmica| Especifique a hora de tempo em segundos. O atraso máximo para as tentativas internas quando se encontram falhas. |
|MaxOperationTimeout |Tempo em segundos, padrão é MaxValue |Dinâmica| Especifique a hora de tempo em segundos. O prazo máximo global para operações de processamento interno no ClusterManager. |
|MaxTimeoutRetryBuffer | Tempo em segundos, padrão é 600 |Dinâmica|Especifique a hora de tempo em segundos. O prazo máximo de funcionamento quando se reexperimenta internamente devido a intervalos de tempo é `<Original Time out> + <MaxTimeoutRetryBuffer>`. O tempo adicional é adicionado em incrementos de MinOperationTimeout. |
|MinOperationTimeout | Tempo em segundos, padrão é 60 |Dinâmica|Especifique a hora de tempo em segundos. O tempo mínimo global para operações de processamento interno no ClusterManager. |
|MinReplicaSetSize |Int, padrão é 3 |Não é permitido|O MinReplicaSetSize para ClusterManager. |
|PlacementConstraints | cadeia, padrão é "" |Não é permitido|Os Constrangimentos de Colocação para ClusterManager. |
|QuorumLossWaitDuration |Tempo em segundos, padrão é MaxValue |Não é permitido| Especifique a hora de tempo em segundos. O QuorumLossWaitDuration para ClusterManager. |
|ReplicaRestartWaitDuration |Tempo em segundos, predefinido é (60,0 \* 30)|Não é permitido|Especifique a hora de tempo em segundos. A Duração do ReplicaRestartWait para ClusterManager. |
|ReplicaSetCheckTimeoutRollbackOverride |Tempo em segundos, padrão é 1200 |Dinâmica| Especifique a hora de tempo em segundos. Se o ReplicaSetCheckTimeout estiver definido para o valor máximo do DWORD; então é ultrapassado com o valor deste config para efeitos de retrocesso. O valor utilizado para o roll-forward nunca é ultrapassado. |
|SkipRollbackUpdateDefaultService | Bool, o padrão é falso. |Dinâmica|O CM saltará para reverter os serviços padrão atualizados durante a reversão da atualização da aplicação. |
|StandByReplicaKeepDuration | Tempo em segundos, predefinido é (3600,0 \* 2)|Não é permitido|Especifique a hora de tempo em segundos. O StandByReplicaKeepDuration para ClusterManager. |
|TargetReplicaSetSize |int, padrão é 7 |Não é permitido|O TargetReplicaSetSize para ClusterManager. |
|UpgradeHealthCheckInterval |Tempo em segundos, padrão é 60 |Dinâmica|A frequência dos controlos do estado de saúde durante uma atualização de aplicações monitorizada |
|UpgradeStatusPollInterval |Tempo em segundos, padrão é 60 |Dinâmica|A frequência das sondagens para o estado de atualização da aplicação. Este valor determina a taxa de atualização para qualquer chamada GetApplicationUpgradeProgress |
|Pedido de Cliente Completo | Bool, o padrão é falso. |Dinâmica| Complete o pedido do cliente quando aceite pela CM. |

## <a name="common"></a>Common

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|PerfMonitorInterval |Tempo em segundos, padrão é 1 |Dinâmica|Especifique a hora de tempo em segundos. Intervalo de monitorização de desempenho. A regulação para 0 ou o valor negativo desativa a monitorização. |

## <a name="defragmentationemptynodedistributionpolicy"></a>DefragmentationEmptyNodeDistributionPolicy
| **Parâmetro** | **Valores Permitidos** |**Política de Upgrade**| **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|PropertyGroup|KeyIntegerValueMap, padrão é Nenhum|Dinâmica|Especifica a desfragmentação da política quando esvazia os nódosos. Para uma determinada métrica 0 indica que a SF deve tentar desfragmentar os nós uniformemente através de UDs e FDs; 1 indica apenas que os nódosos devem ser desfragmentados |

## <a name="defragmentationmetrics"></a>DesfragmentaçãoMétrica
| **Parâmetro** | **Valores Permitidos** |**Política de Upgrade**| **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|PropertyGroup|KeyBoolValueMap, padrão é Nenhum|Dinâmica|Determina o conjunto de métricas que devem ser utilizadas para a desfragmentação e não para o equilíbrio de carga. |

## <a name="defragmentationmetricspercentornumberofemptynodestriggeringthreshold"></a>DefragmentationMetricsPercentOrNumberOfEmptyNodesTriggeringThreshold
| **Parâmetro** | **Valores Permitidos** |**Política de Upgrade**| **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, padrão é Nenhum|Dinâmica|Determina o número de nós livres que são necessários para considerar o cluster desfragmentado, especificando ou por cento no intervalo [0.0 - 1.0] ou número de nós vazios como número >= 1.0 |

## <a name="diagnostics"></a>Diagnóstico

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|AdminOnlyHttpAudit |Bool, o padrão é verdade | Dinâmica | Excluir pedidos HTTP que não afetem o estado do cluster da auditoria. Atualmente; Apenas são excluídos os pedidos do tipo "GET"; mas isto está sujeito a mudanças. |
|AppDiagnosticStoreAccessRequiresImpersonation |Bool, o padrão é verdade | Dinâmica |É ou não necessária a personificação ao aceder às lojas de diagnóstico em nome da aplicação. |
|AppEtwTraceDeletionAgeInDays |Int, padrão é 3 | Dinâmica |Número de dias após o qual apagamos ficheiros ETL antigos contendo vestígios eTW da aplicação. |
|ApplicationLogsFormatVersion |int, padrão é 0 | Dinâmica |Versão para formato de registos de aplicações. Os valores suportados são 0 e 1. A versão 1 inclui mais campos do recorde de eventos Da ETW do que a versão 0. |
|AuditoriaHttpRequests |Bool, o padrão é falso. | Dinâmica | Ligue ou desligue a auditoria http. O objetivo da auditoria é ver as atividades que foram realizadas contra o cluster; incluindo quem iniciou o pedido. Note que esta é a melhor tentativa de exploração madeireira; e a perda de vestígios pode ocorrer. Os pedidos HTTP com autenticação "Utilizador" não são registados. |
|CaptureHttpTelemetry|Bool, o padrão é verdade | Dinâmica | Ligue ou desligue a telemetria HTTP. O objetivo da telemetria é que o Tecido de Serviço seja capaz de capturar dados de telemetria para ajudar a planear trabalhos futuros e identificar áreas problemáticas. A telemetria não regista quaisquer dados pessoais ou o organismo de pedido. A telemetria captura todos os pedidos http, salvo configuração em contrário. |
|ClusterId |String | Dinâmica |A identificação única do aglomerado. Isto é gerado quando o cluster é criado. |
|Casos de Consumo |String | Dinâmica |A lista de casos de consumidores da DCA. |
|DiskFullSafetySpaceInMB |Int, padrão é 1024 | Dinâmica |Espaço restante do disco em MB para proteger da utilização por DCA. |
|EnableCircularTraceSession |Bool, o padrão é falso. | Estático |A bandeira indica se devem ser utilizadas sessões de rastreio circulares. |
|EnablePlatformEventsFilesink |Bool, o padrão é falso. | Estático |Ativar/Desativar eventos da plataforma que estão a ser escritos em disco |
|Ativar telemetria |Bool, o padrão é verdade | Dinâmica |Isto vai ativar ou desativar a telemetria. |
|FailuresOnlyHttpTelemetry | Bool, o padrão é falso. | Dinâmica | Se a captura de telemetria HTTP estiver ativada; capturar apenas pedidos falhados. Isto é para ajudar a reduzir o número de eventos gerados para a telemetria. |
|HttpTelemetryCapturePercentage | int, padrão é 50 | Dinâmica | Se a captura de telemetria HTTP estiver ativada; capturar apenas uma percentagem aleatória de pedidos. Isto é para ajudar a reduzir o número de eventos gerados para a telemetria. |
|MaxDiskQuotaInMB |Int, padrão é 65536 | Dinâmica |Quota de disco em MB para ficheiros de registo windows Fabric. |
|Exemplos de Produtores |String | Dinâmica |A lista de casos de produtores da DCA. |

## <a name="dnsservice"></a>Serviço Dns
| **Parâmetro** | **Valores Permitidos** |**Política de Upgrade**| **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|EnablePartitionedQuery|Bool, o padrão é FALSO|Estático|A bandeira para permitir o apoio às consultas de DNS para serviços divididos. A funcionalidade é desligada por defeito. Para mais informações, consulte [o Serviço de DNS de Tecido de Serviço.](service-fabric-dnsservice.md)|
|InstanceCount|int, padrão é -1|Estático|O valor predefinido é -1, o que significa que o DnsService está a funcionar em cada nó. O OneBox precisa que isto seja definido para 1, uma vez que o DnsService utiliza a conhecida porta 53, pelo que não pode ter múltiplas instâncias na mesma máquina.|
|IsEnabled|Bool, o padrão é FALSO|Estático|Ativa/desativa o DnsService. O DnsService é desativado por defeito e este config precisa de ser definido para o ativar. |
|PartitionPrefix|cadeia, padrão é "--|Estático|Controla o valor de cadeia de prefixo de divisória em consultas de DNS para serviços divididos. O valor: <ul><li>Deve ser compatível com rFC, uma vez que fará parte de uma consulta do DNS.</li><li>Não deve conter um ponto, '.', uma vez que o ponto interfere com o comportamento do sufixo DNS.</li><li>Não deve ter mais de 5 caracteres.</li><li>Não pode ser uma corda vazia.</li><li>Se a definição partitionPrefix estiver sobremontada, então partitionSuffix deve ser sobrermontado, e vice-versa.</li></ul>Para mais informações, consulte [service Fabric DNS Service.](service-fabric-dnsservice.md). .|
|PartitionSSuffix|cadeia, padrão é ""|Estático|Controla o valor de cadeia de sufixo de divisória em consultas de DNS para serviços divididos. O valor: <ul><li>Deve ser compatível com rFC, uma vez que fará parte de uma consulta do DNS.</li><li>Não deve conter um ponto, '.', uma vez que o ponto interfere com o comportamento do sufixo DNS.</li><li>Não deve ter mais de 5 caracteres.</li><li>Se a definição partitionPrefix estiver sobremontada, então partitionSuffix deve ser sobrermontado, e vice-versa.</li></ul>Para mais informações, consulte [service Fabric DNS Service.](service-fabric-dnsservice.md). . |

## <a name="eventstoreservice"></a>EventStoreService

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|MinReplicaSetSize|int, padrão é 0|Estático|O MinReplicaSetSize para o serviço EventStore |
|PlacementConstraints|cadeia, padrão é ""|Estático|  Os Constrangimentos de Colocação para o serviço EventStore |
|TargetReplicaSetSize|int, padrão é 0|Estático| O TargetReplicaSetSize para o serviço EventStore |

## <a name="fabricclient"></a>FabricClient

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|ConnectionInitializationTimeout |Tempo em segundos, padrão é 2 |Dinâmica|Especifique a hora de tempo em segundos. Intervalo de tempo de ligação para cada vez que o cliente tenta abrir uma ligação ao gateway.|
|HealthOperationTimeout |Tempo em segundos, padrão é 120 |Dinâmica|Especifique a hora de tempo em segundos. O tempo de paragem para uma mensagem de relatório enviada ao Health Manager. |
|HealthReportRetrySendInterval |Tempo em segundos, padrão é 30, mínimo é 1 |Dinâmica|Especifique a hora de tempo em segundos. O intervalo em que o componente de reporte envia relatórios de saúde acumulados ao Health Manager. |
|HealthReportSendInterval |Tempo em segundos, padrão é 30 |Dinâmica|Especifique a hora de tempo em segundos. O intervalo em que a componente de reporte envia relatórios de saúde acumulados ao Health Manager. |
|KeepAliveIntervalInSeconds |int, padrão é 20 |Estático|O intervalo em que o transporte FabricClient envia mensagens vivas para o portal. Para 0; manterAlive está incapacitado. Deve ser um valor positivo. |
|MaxFileSenderThreads |Uint, o padrão é 10 |Estático|O número máximo de ficheiros que são transferidos em paralelo. |
|NodeAddresses |cadeia, padrão é "" |Estático|Uma coleção de endereços (cordas de ligação) em diferentes nós que podem ser usados para comunicar com o Serviço de Nomeação. Inicialmente, o Cliente liga a seleção de um dos endereços aleatoriamente. Se for fornecida mais de uma cadeia de ligação e se uma ligação falhar devido a um erro de comunicação ou de tempo indevido; o Cliente muda para utilizar o endereço seguinte sequencialmente. Consulte a secção de retry do Endereço de Serviço de Nomeação para obter mais informações sobre a semântica de retries. |
|PartitionLocationCacheLimit |Int, padrão é 100000 |Estático|Número de divisórias em cache para resolução de serviço (definida para 0 sem limite). |
|RetryBackoffInterval |Tempo em segundos, padrão é 3 |Dinâmica|Especifique a hora de tempo em segundos. O intervalo de back-off antes de voltar a tentar a operação. |
|ServiceChangePollInterval |Tempo em segundos, padrão é 120 |Dinâmica|Especifique a hora de tempo em segundos. O intervalo entre sondagens consecutivas para alterações de serviço do cliente para a porta de entrada para notificações de alteração de serviço registadas. |

## <a name="fabrichost"></a>FabricHost

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|ActivationMaxFailureCount |Int, padrão é 10 |Dinâmica|Esta é a contagem máxima para a qual o sistema irá voltar a tentar a ativação falhada antes de desistir. |
|ActivationMaxRetryInterval |Tempo em segundos, padrão é 300 |Dinâmica|Especifique a hora de tempo em segundos. Intervalo de repetição máximo para ativação. Em cada falha contínua, o intervalo de repetição é calculado como Min(ActivationMaxRetryInterval; Contagem contínua de falhas * ActivaçãoRetryBackoffInterval). |
|ActivationRetryBackoffInterval |Tempo em segundos, padrão é 5 |Dinâmica|Especifique a hora de tempo em segundos. Intervalo de backoff em cada falha de ativação;Em cada falha de ativação contínua, o sistema irá voltar a tentar a ativação até ao Número de Ativações MaxActivações. O intervalo de repetição em cada tentativa é um produto de falha contínua de ativação e do intervalo de back-off de ativação. |
|EnableRestartManagement |Bool, o padrão é falso. |Dinâmica|Isto é para ativar o reinício do servidor. |
|EnableServiceFabricAutomaticUpdates |Bool, o padrão é falso. |Dinâmica|Isto é para ativar a atualização automática do tecido através do Windows Update. |
|EnableServiceFabricBaseUpgrade |Bool, o padrão é falso. |Dinâmica|Isto é para ativar a atualização base para o servidor. |
|FailureReportingExpedidReportingIntervalEnabled | Bool, o padrão é verdade | Estático | Permite taxas de carregamento mais rápidas em DCA quando o FabricHost está no modo de Reporte de Falhas. |
|FalhaReportingTimeout | TimeSpan, o padrão é comum:TimeSpan:FromSeconds (60) | Estático |Especifique a hora de tempo em segundos. Timeout para reportagens de falhas da DCA no caso FabricHost encontra uma falha inicial de arranque. | 
|RunDCAOnStartupFailure | Bool, o padrão é verdade | Estático |Determina se vai lançar a DCA para carregar registos quando enfrentar problemas de arranque no FabricHost. | 
|Início Timeout |Tempo em segundos, padrão é 300 |Dinâmica|Especifique a hora de tempo em segundos. Tempo para arranque de gestor de ativação de tecidos. |
|StopTimeout |Tempo em segundos, padrão é 300 |Dinâmica|Especifique a hora de tempo em segundos. O prazo para a ativação do serviço hospedado; desativação e atualização. |

## <a name="fabricnode"></a>Nó de Tecido

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|ClientAuthX509FindType |cadeia, padrão é "FindByThumbprint" |Dinâmica|Indica como procurar um certificado na loja especificado pelo valor suportado do Nome de Loja ClienteAuthX509: FindByThumbprint; FindBySubjectName. |
|ClientAuthX509FindValue |cadeia, padrão é "" | Dinâmica|Valor do filtro de pesquisa usado para localizar certificado para papel de administrador padrão FabricClient. |
|ClientAuthX509FindValueSecondary |cadeia, padrão é "" |Dinâmica|Valor do filtro de pesquisa usado para localizar certificado para papel de administrador padrão FabricClient. |
|ClientAuthX509StoreName |cadeia, padrão é "O meu" |Dinâmica|Nome da loja de certificados X.509 que contém certificado para papel de administrador padrão FabricClient. |
|ClusterX509FindType |cadeia, padrão é "FindByThumbprint" |Dinâmica|Indica como procurar o certificado de cluster na loja especificada pelo ClusterX509StoreName Valores suportados: "FindByThumbprint"; "FindbySubjectName" com "FindBySubjectName"; quando há vários fósforos; O que tem a expiração mais distante é usado. |
|ClusterX509FindValue |cadeia, padrão é "" |Dinâmica|Valor do filtro de pesquisa usado para localizar o certificado de cluster. |
|ClusterX509FindValueSecondary |cadeia, padrão é "" |Dinâmica|Valor do filtro de pesquisa usado para localizar o certificado de cluster. |
|ClusterX509StoreName |cadeia, padrão é "O meu" |Dinâmica|Nome do certificado X.509 que contém certificado de cluster para garantir comunicação intra-cluster. |
|EndApplicationPortRange |int, padrão é 0 |Estático|Fim (sem inclusivo) das portas de aplicação geridas por subsistema de hospedagem. Necessário se endpointFilteringEnabled for verdadeiro no hospedagem. |
|ServerAuthX509FindType |cadeia, padrão é "FindByThumbprint" |Dinâmica|Indica como procurar o certificado de servidor na loja especificada pelo valor suportado pelo ServerAuthX509StoreName: FindByThumbprint; FindBySubjectName. |
|ServerAuthX509FindValue |cadeia, padrão é "" |Dinâmica|Valor do filtro de pesquisa usado para localizar o certificado do servidor. |
|ServerAuthX509FindValueSecondary |cadeia, padrão é "" |Dinâmica|Valor do filtro de pesquisa usado para localizar o certificado do servidor. |
|ServerAuthX509StoreName |cadeia, padrão é "O meu" |Dinâmica|Nome da loja de certificados X.509 que contém certificado de servidor para serviço de entrada. |
|StartApplicationPortRange |int, padrão é 0 |Estático|Início das portas de aplicação geridas por subsistema de hospedagem. Necessário se endpointFilteringEnabled for verdadeiro no hospedagem. |
|StateTraceInterval |Tempo em segundos, padrão é 300 |Estático|Especifique a hora de tempo em segundos. O intervalo para rastrear o estado do nó em cada nó e até os nós em FM/FMM. |
|UserRoleClientX509FindType |cadeia, padrão é "FindByThumbprint" |Dinâmica|Indica como procurar um certificado na loja especificado pelo userRoleClientX509StoreName Valor suportado: FindByThumbprint; FindBySubjectName. |
|UserRoleClientX509FindValue |cadeia, padrão é "" |Dinâmica|Valor do filtro de pesquisa usado para localizar certificado para função de utilizador padrão FabricClient. |
|UserRoleClientX509FindValueSecondary |cadeia, padrão é "" |Dinâmica|Valor do filtro de pesquisa usado para localizar certificado para função de utilizador padrão FabricClient. |
|UserRoleClientX509StoreName |cadeia, padrão é "O meu" |Dinâmica|Nome da loja de certificados X.509 que contém certificado para função de utilizador padrão FabricClient. |

## <a name="failovermanager"></a>FailoverManager

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|AllowNodeStateRemovedForSeedNode|Bool, o padrão é FALSO |Dinâmica|Bandeira para indicar se é permitido remover o estado do nó para um nó de sementes |
|BuildReplicaTimeLimit|TimeSpan, o padrão é comum:TimeSpan:FromSeconds (3600)|Dinâmica|Especifique a hora de tempo em segundos. O prazo para a construção de uma réplica imponente; após o qual será iniciado um relatório de saúde de alerta |
|ClusterPauseThreshold|Int, padrão é 1|Dinâmica|Se o número de nós no sistema ficar abaixo deste valor, então a colocação; equilíbrio de carga; e a falha é interrompida. |
|CreateInstanceTimeLimit|TimeSpan, o padrão é comum:TimeSpan:FromSeconds (300)|Dinâmica|Especifique a hora de tempo em segundos. O prazo para a criação de um caso apátrida; após o qual será iniciado um relatório de saúde de alerta |
|ExpectedClusterSize|Int, padrão é 1|Dinâmica|Quando o cluster é inicialmente iniciado; o FM esperará que estes nós se reportem antes de começar a colocar outros serviços; incluindo os serviços do sistema como nomear. Aumentar este valor aumenta o tempo que um cluster leva para iniciar; mas impede que os primeiros nós fiquem sobrecarregados e também os movimentos adicionais que serão necessários à medida que mais nós entram online. Este valor deve geralmente ser definido para uma pequena fração do tamanho inicial do cluster. |
|ExpectedNodeDeactivationDuration|TimeSpan, o padrão é comum:TimeSpan::FromSeconds (60.0 \* 30)|Dinâmica|Especifique a hora de tempo em segundos. Esta é a duração esperada para um nó para completar a desativação. |
|ExpectedNodeFabricUpgradeDuration|TimeSpan, o padrão é comum:TimeSpan::FromSeconds (60.0 \* 30)|Dinâmica|Especifique a hora de tempo em segundos. Esta é a duração esperada para um nó ser atualizado durante a atualização do Windows Fabric. |
|ExpectedReplicaUpgradeDuration|TimeSpan, o padrão é comum:TimeSpan::FromSeconds (60.0 \* 30)|Dinâmica|Especifique a hora de tempo em segundos. Esta é a duração esperada para todas as réplicas serem atualizadas num nó durante a atualização da aplicação. |
|IsSingletonReplicaMoveAllowedDuringUpgrade|Bool, o padrão é VERDADE|Dinâmica|Se for definido como verdadeiro; réplicas com um conjunto de réplica de 1 será permitido mover-se durante a atualização. |
|MinReplicaSetSize|Int, padrão é 3|Não é permitido|Este é o tamanho mínimo da réplica definida para o FM. Se o número de réplicas ativas de FM descer abaixo deste valor; o FM rejeitará alterações ao cluster até que pelo menos o número de réplicas min seja recuperado |
|PlacementConstraints|cadeia, padrão é ""|Não é permitido|Quaisquer restrições de colocação para as réplicas do gestor de failover |
|PlacementTimeLimit|TimeSpan, o padrão é comum:TimeSpan::FromSeconds (600)|Dinâmica|Especifique a hora de tempo em segundos. O prazo para atingir a contagem de réplicas alvo; após o qual será iniciado um relatório de saúde de alerta |
|QuorumLossWaitDuration |Tempo em segundos, padrão é MaxValue |Dinâmica|Especifique a hora de tempo em segundos. Esta é a duração máxima para a qual permitimos que uma partição esteja em estado de perda de quórum. Se a partição ainda estiver em perda de quórum após esta duração; a partição é recuperada da perda de quórum considerando as réplicas para baixo como perdidas. Note que isto pode potencialmente incorrer na perda de dados. |
|ReconfigurationTimeLimit|TimeSpan, o padrão é comum:TimeSpan:FromSeconds (300)|Dinâmica|Especifique a hora de tempo em segundos. O prazo para a reconfiguração; após o qual será iniciado um relatório de saúde de alerta |
|ReplicaRestartWaitDuration|TimeSpan, o padrão é comum:TimeSpan::FromSeconds (60.0 \* 30)|Não é permitido|Especifique a hora de tempo em segundos. Esta é a ReplicaRestartWaitDuration para o FMService |
| SeedNodeQuorumAdditionalBufferNodes | int, padrão é 0 | Dinâmica | Tampão de nós de sementes que é necessário para ser para cima (juntamente com quórum de nós de sementes) FM deve permitir que um máximo de nódeos de sementes (totalNodequorum + seedNodeQuorumAdicionalBufferNodes)) deve permitir que um máximo de nódeos de sementes (totalNodequorum + seedNodeumAdicionalBufferNodes)) desça. |
|StandByReplicaKeepDuration|Timepan, predefinido é comum:TimeSpan::FromSeconds (3600.0 \* 24 \* 7)|Não é permitido|Especifique a hora de tempo em segundos. Esta é a duração standByReplicaKeep para o FMService |
|TargetReplicaSetSize|int, padrão é 7|Não é permitido|Este é o número-alvo de réplicas de FM que o Windows Fabric irá manter. Um número mais elevado resulta numa maior fiabilidade dos dados fm; com uma pequena troca de desempenho. |
|UserMaxStandByReplicaCount |Int, padrão é 1 |Dinâmica|O número máximo padrão de réplicas De StandBy que o sistema mantém para os serviços de utilizador. |
|UserReplicaRestartWaitDuration |Tempo em segundos, padrão é 60.0 \* 30 |Dinâmica|Especifique a hora de tempo em segundos. Quando uma réplica persistida desce; O Windows Fabric aguarda esta duração para que a réplica volte a subir antes de criar novas réplicas de substituição (que exigiriam uma cópia do estado). |
|UserStandByReplicaKeepDuration |Tempo em segundos, o padrão é de 3600,0 \* 24 \* 7 |Dinâmica|Especifique a hora de tempo em segundos. Quando uma réplica persistida volta de um estado baixo; pode já ter sido substituído. Este temporizador determina quanto tempo o FM manterá a réplica de espera antes de a descartar. |

## <a name="faultanalysisservice"></a>Serviço de Análise de Falhas

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|CompletedActionKeepDurationInSeconds | Int, padrão é 604800 |Estático| Este é aproximadamente quanto tempo para manter as ações que estão em estado terminal. Isto também depende do StoredActionCleanupIntervalInSeconds; uma vez que o trabalho de limpeza só é feito nesse intervalo. 604800 é 7 dias. |
|DataLossCheckPollIntervalInSeconds|int, padrão é 5|Estático|Este é o tempo entre os controlos que o sistema executa enquanto espera que a perda de dados aconteça. O número de vezes que o número de perda de dados será verificado por iteração interna é DataLossCheckWaitDurationInSeconds/this. |
|DataLossCheckWaitDurationInSeconds|int, padrão é 25|Estático|O tempo total; em segundos; que o sistema vai esperar que a perda de dados aconteça. Isto é utilizado internamente quando o api StartPartitionDataLossAsync() é chamado. |
|MinReplicaSetSize |int, padrão é 0 |Estático|O MinReplicaSetSize para FaultAnalysisService. |
|PlacementConstraints | cadeia, padrão é ""|Estático| Os Limites de Colocação para Serviço de Análise de Falhas. |
|QuorumLossWaitDuration | Tempo em segundos, padrão é MaxValue |Estático|Especifique a hora de tempo em segundos. O QuorumLossWaitDura para O Serviço de Análise de Falhas. |
|ReplicaDropWaitDurationInSeconds|int, padrão é 600|Estático|Este parâmetro é utilizado quando o api perda de dados é chamado. Controla o tempo que o sistema espera que uma réplica seja largada após a remoção da réplica ser ininvocada internamente nele. |
|ReplicaRestartWaitDuration |Tempo em segundos, padrão é de 60 minutos|Estático|Especifique a hora de tempo em segundos. A duração da replicaRestartWait para FaultAnalysisService. |
|StandByReplicaKeepDuration| Tempo em segundos, predefinido é (60*24*7) minutos |Estático|Especifique a hora de tempo em segundos. O StandByReplicaKeepDura para O Serviço de Análise de Falhas. |
|StoredActionCleanupIntervalInSeconds | Int, padrão é 3600 |Estático|É a frequência com que a loja será limpa. Apenas ações em estado terminal; e que concluído pelo menos ConcluídoActionKeepDurationInSeconds atrás será removido. |
|StoredChaosEventCleanupIntervalInSeconds | Int, padrão é 3600 |Estático|É assim que a loja será auditada para limpeza; se o número de eventos for superior a 30000; a limpeza vai começar. |
|TargetReplicaSetSize |int, padrão é 0 |Estático|NOT_PLATFORM_UNIX_START O TargetReplicaSetSize para FaultAnalysisService. |

## <a name="federation"></a>de Federação

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|LeaseDuration |Tempo em segundos, padrão é 30 |Dinâmica|Duração que um arrendamento dura entre um nó e seus vizinhos. |
|LeaseDurationAcrossFaultDomain |Tempo em segundos, padrão é 30 |Dinâmica|Duração que um arrendamento dura entre um nó e seus vizinhos através de domínios de falhas. |

## <a name="filestoreservice"></a>FileStoreService

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|AcceptChunkUpload|Bool, o padrão é VERDADE|Dinâmica|A Config para determinar se o serviço de loja de ficheiros aceita o upload de ficheiros baseado em pedaços ou não durante o pacote de aplicação de cópias. |
|AnonymousAccessEnabled | Bool, o padrão é verdade |Estático|Ativar/Desativar o acesso anónimo às ações do FileStoreService. |
|CommonName1Ntlmx509CommonName|cadeia, padrão é ""|Estático| O nome comum do certificado X509 usado para gerar HMAC no CommonName1NtlmPasswordSecret ao utilizar a autenticação NTLM |
|CommonName1Ntlmx509StoreLocation|cadeia, padrão é "LocalMachine"|Estático|A localização da loja do certificado X509 usado para gerar HMAC no CommonName1NtlmPasswordSecret ao utilizar a autenticação NTLM |
|CommonName1Ntlmx509StoreName|cadeia, padrão é "MY"| Estático|O nome da loja do certificado X509 usado para gerar HMAC no CommonName1NtlmPasswordSecret ao utilizar a autenticação NTLM |
|CommonName2Ntlmx509CommonName|cadeia, padrão é ""|Estático|O nome comum do certificado X509 usado para gerar HMAC no CommonName2NtlmPasswordSecret ao utilizar a autenticação NTLM |
|CommonName2Ntlmx509StoreLocation|cadeia, padrão é "LocalMachine"| Estático|A localização da loja do certificado X509 usado para gerar HMAC no CommonName2NtlmPasswordSecret ao utilizar a autenticação NTLM |
|CommonName2Ntlmx509StoreName|cadeia, padrão é "MY"|Estático| O nome da loja do certificado X509 usado para gerar HMAC no CommonName2NtlmPasswordSecret ao utilizar a autenticação NTLM |
|CommonNameNtlmPasswordSecret|SecureString, predefinido é comum:SecureString(")| Estático|O segredo da palavra-passe que usava como semente para gerar a mesma senha ao usar a autenticação NTLM |
|DiskSpaceHealthReportingIntervalwhenclosetooutofdiskSpace |TimeSpan, o padrão é comum:TimeSpan::FromMinutes (5)|Dinâmica|Especifique a hora de tempo em segundos. O intervalo de tempo entre a verificação do espaço do disco para reportar evento de saúde quando o disco está perto do espaço. |
|DiskSpaceHealthReportingIntervalwhenEnoughdiskSpace |TimeSpan, o padrão é comum:TimeSpan:FromMinutes(15)|Dinâmica|Especifique a hora de tempo em segundos. O intervalo de tempo entre a verificação do espaço do disco para reportar evento de saúde quando há espaço suficiente no disco. |
|EnableImageStoreHealthReporting |Bool, o padrão é VERDADE |Estático|Config para determinar se o serviço de loja de arquivos deve reportar a sua saúde. |
|FreediskSpaceNotificationSizeInKB|int64, o padrão é 25\*1024 |Dinâmica|O tamanho do espaço de disco livre abaixo do qual pode ocorrer um aviso sanitário. O valor mínimo deste config e do FreeDiskSpaceNotificationThresholdPercentage são utilizados para determinar o envio do aviso sanitário. |
|FreediskSpaceNotificationThresholdPercentage|duplo, padrão é 0.02 |Dinâmica|A percentagem de espaço de disco livre abaixo do qual pode ocorrer um aviso sanitário. O valor mínimo deste config e do FreeDiskSpaceNotificationInMB config são utilizados para determinar o envio de avisode saúde. |
|GenerateV1CommonNameAccount| Bool, o padrão é VERDADE|Estático|Especifica se gera uma conta com algoritmo de geração V1 de nome de utilizador. Começando com a versão 6.1 do Tecido de Serviço; uma conta com geração v2 é sempre criada. A conta V1 é necessária para atualizações de/para versões que não suportam a geração V2 (antes de 6.1).|
|MaxCopyOperationThreads | uint, padrão é 0 |Dinâmica| O número máximo de ficheiros paralelos que o secundário pode copiar a partir das primárias. '0' == número de núcleos. |
|MaxFileOperationThreads | Uint, o padrão é 100 |Estático| O número máximo de fios paralelos autorizados a executar FileOperations (Copy/Move) no primário. '0' == número de núcleos. |
|MaxRequestProcessingThreads | Uint, padrão é 200 |Estático|O número máximo de fios paralelos autorizados a processar pedidos nas primárias. '0' == número de núcleos. |
|MaxSecondaryFileCopyFailureThreshold | Uint, o padrão é 25|Dinâmica|O número máximo de cópias de ficheiros retenta no secundário antes de desistir. |
|MaxStoreOperations | Uint, o padrão é 4096 |Estático|O número máximo de operações de transação paralelas permitidas nas primárias. '0' == número de núcleos. |
|NamingOperationTimeout |Tempo em segundos, padrão é 60 |Dinâmica|Especifique a hora de tempo em segundos. O tempo para executar a operação de nomeação. |
|PrimaryAccountNTLMPasswordSecret | SecureString, o padrão está vazio |Estático| O segredo da palavra-passe que usava como semente para gerar a mesma palavra-passe ao utilizar a autenticação NTLM. |
|PrimaryAccountNTLMX509StoreLocation | cadeia, padrão é "LocalMachine"|Estático| A localização da loja do certificado X509 usado para gerar HMAC no PrimaryAccountNTLMPasswordSecret ao utilizar a autenticação NTLM. |
|PrimaryAccountNTLMX509StoreName | cadeia, padrão é "MY"|Estático| O nome da loja do certificado X509 usado para gerar HMAC no PrimaryAccountNTLMPasswordSecret ao utilizar a autenticação NTLM. |
|PrimaryAccountNTLMX509Thumbprint | cadeia, padrão é ""|Estático|A impressão digital do certificado X509 usado para gerar HMAC no PrimaryAccountNTLMPasswordSecret ao utilizar a autenticação NTLM. |
|PrimaryAccountType | cadeia, padrão é "" |Estático|A conta primáriaType do principal para ACL as ações FileStoreService. |
|PrimaryAccountUserName | cadeia, padrão é "" |Estático|A conta principal Username do principal para ACL as ações FileStoreService. |
|PrimaryAccountUserPassword | SecureString, o padrão está vazio |Estático|A palavra-passe da conta primária do principal para acl as ações do FileStoreService. |
|QueryOperationTimeout | Tempo em segundos, padrão é 60 |Dinâmica|Especifique a hora de tempo em segundos. O tempo para realizar a operação de consulta. |
|SecondaryAccountNTLMPasswordSecret | SecureString, o padrão está vazio |Estático| O segredo da palavra-passe que usava como semente para gerar a mesma palavra-passe ao utilizar a autenticação NTLM. |
|SecondaryAccountNTLMX509StoreLocation | cadeia, padrão é "LocalMachine" |Estático|A localização da loja do certificado X509 utilizado para gerar HMAC no SecondaryAccountNTLMPasswordSecret ao utilizar a autenticação NTLM. |
|SecondaryAccountNTLMX509StoreName | cadeia, padrão é "MY" |Estático|O nome da loja do certificado X509 usado para gerar HMAC no SecondaryAccountNTLMPasswordSecret ao utilizar a autenticação NTLM. |
|SecondaryAccountNTLMX509Thumbprint | cadeia, padrão é ""| Estático|A impressão digital do certificado X509 usado para gerar HMAC no SecondaryAccountNTLMPasswordSecret ao utilizar a autenticação NTLM. |
|SecondaryAccountType | cadeia, padrão é ""|Estático| O Tipo de Conta secundária do principal para ACL as ações do FileStoreService. |
|SecondaryAccountUserName | cadeia, padrão é ""| Estático|A conta secundária Username do principal para ACL as ações fileStoreService. |
|SecondaryAccountUserPassword | SecureString, o padrão está vazio |Estático|A senha de conta secundária do principal para ACL as ações do FileStoreService. |
|SecondaryFileCopyRetryDelayMilliseconds|uint, padrão é 500|Dinâmica|O atraso de retry da cópia do ficheiro (em milissegundos).|
|UseChunkContentInTransportMessage|Bool, o padrão é VERDADE|Dinâmica|A bandeira para a utilização da nova versão do protocolo de upload introduzido em v6.4. Esta versão protocolar utiliza o transporte de tecido de serviço para carregar ficheiros para uma loja de imagens que proporciona um melhor desempenho do que o protocolo SMB utilizado em versões anteriores. |

## <a name="healthmanager"></a>HealthManager

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|EnableApplicationTypeHealthEvaluation |Bool, o padrão é falso. |Estático|Política de avaliação da saúde do cluster: permitir avaliação de saúde por tipo de aplicação. |
|MaxSuggestedNumberOfEntityHealthReports|Int, padrão é 100 |Dinâmica|O número máximo de relatórios de saúde que uma entidade pode ter antes de levantar preocupações sobre a lógica de relatório de saúde do cão de guarda. Cada entidade de saúde deve ter um número relativamente pequeno de relatórios de saúde. Se a contagem do relatório for superior a este número; pode haver problemas com a implementação do cão de guarda. Uma entidade com demasiados relatórios é sinalizada através de um relatório de saúde de alerta quando a entidade é avaliada. |

## <a name="healthmanagerclusterhealthpolicy"></a>HealthManager/ClusterHealthPolicy

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|ConsiderWarningAsError |Bool, o padrão é falso. |Estático|Política de avaliação da saúde do cluster: os avisos são tratados como erros. |
|MaxPercentUnhealthyApplications | int, padrão é 0 |Estático|Política de avaliação da saúde do cluster: por cento máximo das aplicações não saudáveis permitidas para que o cluster seja saudável. |
|MaxPercentUnhealthyNodes | int, padrão é 0 |Estático|Política de avaliação da saúde do cluster: por cento máximo dos nós não saudáveis permitidos para que o cluster seja saudável. |

## <a name="healthmanagerclusterupgradehealthpolicy"></a>HealthManager/ClusterUpgradeHealthPolicy

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|MaxPercentDeltaUnhealthyNodes|Int, padrão é 10|Estático|Política de avaliação da saúde de cluster: por cento máximo dos nós delta não saudáveis permitidos para que o cluster seja saudável |
|MaxPercentUpgradeDomainDeltaUnhealthyNodes|int, padrão é 15|Estático|Política de avaliação da saúde de upgrade do cluster: por cento máximo do delta dos nós não saudáveis em um domínio de upgrade permitido para que o cluster seja saudável |

## <a name="hosting"></a>Alojamento

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|ActivationMaxFailureCount |Número inteiro, padrão é 10 |Dinâmica|Número de vezes que o sistema retenta a ativação falhada antes de desistir |
|ActivationMaxRetryInterval |Tempo em segundos, padrão é 300 |Dinâmica|Em cada falha de ativação contínua, o sistema retenta a ativação até ao ActivationMaxFailureCount. ActivaçãoMaxRetryInterval especifica intervalo de tempo de espera antes de voltar a tentar após cada falha de ativação |
|ActivationRetryBackoffInterval |Tempo em Segundos, padrão é 5 |Dinâmica|Intervalo de backoff em cada falha de ativação; Em cada falha de ativação contínua, o sistema retenta a ativação até ao Número de Ativações MaxActivações. O intervalo de repetição em cada tentativa é um produto de falha contínua de ativação e do intervalo de back-off de ativação. |
|ActivaçãoTimeout| TimeSpan, o padrão é comum:TimeSpan:FromSeconds (180)|Dinâmica| Especifique a hora de tempo em segundos. O prazo para a ativação da aplicação; desativação e atualização. |
|ApplicationHostCloseTimeout| TimeSpan, o padrão é comum:TimeSpan:FromSeconds (120)|Dinâmica| Especifique a hora de tempo em segundos. Quando a saída do Tecido é detetada num processo auto-activado; O FabricRuntime fecha todas as réplicas no processo de hospedar (applicationhost) do utilizador. Este é o tempo de paragem para uma operação renhida. |
| CnsNetworkPluginCnmUrlPort | wstring, padrão é L"48080" | Estático | Porta de url Azure cnm api |
| CnsNetworkPluginCnsUrlPort | wstring, padrão é L"10090" | Estático | Porta de url Azure cns |
|ContainerServiceArguments|string, padrão é "-H localhost:2375 -H npipe://"|Estático|Service Fabric (SF) gere o daemon do Docker (exceto em máquinas de clientes windows como win10). Esta configuração permite ao utilizador especificar argumentos personalizados que devem ser passados para docker daemon ao iniciá-lo. Quando os argumentos personalizados são especificados, o Service Fabric não passa qualquer outro argumento para o motor Docker exceto o argumento "pidfile". Por conseguinte, os utilizadores não devem especificar o argumento "pidfile" como parte dos seus argumentos de cliente. Além disso, os argumentos personalizados devem garantir que o daemon do Docker ouça o tubo de nome padrão no Windows (ou tomada de domínio Unix no Linux) para que o Tecido de Serviço possa comunicar com ele.|
|ContainerServiceLogFileMaxSizeInKb|int, padrão é 32768|Estático|Tamanho máximo do ficheiro de registo gerado por recipientes de estivador.  Só janelas.|
|ContainerImageDownloadTimeout|int, número de segundos, padrão é de 1200 (20 minutos)|Dinâmica|Número de segundos antes do download do tempo de imagem.|
|ContainerImagesToSkip|cadeia, nomes de imagem separados por caracteres de linha vertical, padrão é ""|Estático|Nome de uma ou mais imagens de contentores que não devem ser eliminadas.  Utilizado com o parâmetro PodruneContainerImages.|
|ContainerServiceLogFileNamePrefix|cadeia, padrão é "sfcontainerlogs"|Estático|Prefixo de nome de ficheiro para ficheiros de registo gerados por recipientes de estivador.  Só janelas.|
|ContainerServiceLogFileRetentionCount|Int, padrão é 10|Estático|Número de ficheiros de registo gerados por recipientes de estivadores antes de os ficheiros de registo serem substituídos.  Só janelas.|
|CreateFabricRuntimeTimeout|TimeSpan, o padrão é comum:TimeSpan:FromSeconds (120)|Dinâmica| Especifique a hora de tempo em segundos. O valor de tempo para a chamada de sincronização FabricCreateRuntime |
|DefaultContainerRepositoryAccountName|cadeia, padrão é ""|Estático|Credenciais predefinidas utilizadas em vez de credenciais especificadas em ApplicationManifest.xml |
|DefaultContainerRepositoryPassword|cadeia, padrão é ""|Estático|Credenciais de senha predefinidas utilizadas em vez de credenciais especificadas em ApplicationManifest.xml|
|DefaultContainerRepositoryPasswordType|cadeia, padrão é ""|Estático|Quando não estiver em cadeia vazia, o valor pode ser "Encriptado" ou "SecretsStoreRef".|
|PadrãoDnsSearchSuffixEmpty|Bool, o padrão é FALSO|Estático|Por predefinição, o nome de serviço é anexado ao nome SF DNS para serviços de contentores. Esta funcionalidade para este comportamento para que nada seja anexado ao nome SF DNS por padrão na via de resolução.|
|DeploymentMaxFailureCount|int, padrão é 20| Dinâmica|A implementação da aplicação será novamente experimentada para os tempos de ImplantaçãoMaxFailureCount antes de falhar a implementação dessa aplicação no nó.| 
|DeploymentMaxRetryInterval| TimeSpan, o padrão é comum:TimeSpan:FromSeconds (3600)|Dinâmica| Especifique a hora de tempo em segundos. Intervalo de repetição máximo para a implantação. Em cada falha contínua, o intervalo de repetição é calculado como Min(DeploymentMaxRetryInterval; Contagem contínua de falhas * DeploymentRetryBackoffInterval) |
|DeploymentRetryBackoffInterval| TimeSpan, o padrão é comum:TimeSpan:FromSeconds (10)|Dinâmica|Especifique a hora de tempo em segundos. Intervalo de back-off para a falha de implantação. Em cada falha de implementação contínua, o sistema irá voltar a tentar a implementação até ao MaxDeploymentFailureCount. O intervalo de repetição é um produto de falha contínua de implantação e do intervalo de dispor de dispor de implantação. |
|Recipientes de desativação|Bool, o padrão é FALSO|Estático|Config para recipientes incapacitantes - usado em vez de DesactivadorDeDorServiceOnContainerActivatorOpen que é config depreciado |
|DisableDockerRequestRetry|Bool, o padrão é FALSO |Dinâmica| Por padrão, a SF comunica com dD (docker dameon) com um tempo de 'DockerRequestTimeout' para cada pedido de http enviado para o mesmo. Se o DD não responder dentro deste período de tempo; A SF reenvia o pedido se a operação de nível superior ainda tiver tempo restante.  Com recipiente de hiperv; DD às vezes leva muito mais tempo para trazer o recipiente ou desativá-lo. Nesses casos, o pedido de DD sai do ponto de vista da SF e a SF retenta a operação. Às vezes isto parece adicionar mais pressão sobre o DD. Este config permite desativar esta nova tentativa e esperar que o DD responda. |
|DnsServerListTwoIps | Bool, o padrão é FALSO | Estático | Estas bandeiras adicionam o servidor local dns duas vezes para ajudar a aliviar problemas de determinação intermitentes. |
| DoNotInjectLocalDnsServer | Bool, o padrão é FALSO | Estático | Impede o tempo de execução para injetar o IP local como servidor DNS para recipientes. |
|EnableActivateNoWindow| Bool, o padrão é FALSO|Dinâmica| O processo ativado é criado em segundo plano sem qualquer consola. |
|EnableContainerServiceDebugMode|Bool, o padrão é VERDADE|Estático|Ativar/desativar a exploração de madeira para os recipientes de estivadores.  Só janelas.|
|EnableDockerHealthCheckIntegration|Bool, o padrão é VERDADE|Estático|Permite a integração de eventos do Docker HEALTHCHECK com relatório de saúde do sistema service Fabric |
|EnableProcessDebugging|Bool, o padrão é FALSO|Dinâmica| Permite o lançamento de anfitriões de aplicações sob debugger |
|EndpointProviderEnabled| Bool, o padrão é FALSO|Estático| Permite a gestão dos recursos endpoint pela Fabric. Requer especificação da gama de portas de aplicação inicial e final em FabricNode. |
|FabricContainerAppsEnabled| Bool, o padrão é FALSO|Estático| |
|FirewallPolicyEnabled|Bool, o padrão é FALSO|Estático| Permite a abertura de portas de firewall para recursos Endpoint com portas explícitas especificadas no ServiceManifest |
|GetCodePackageActivationContextTimeout|TimeSpan, o padrão é comum:TimeSpan:FromSeconds (120)|Dinâmica|Especifique a hora de tempo em segundos. O valor de tempo de tempo para as chamadas CodePackageActivationContext. Isto não é aplicável aos serviços ad hoc. |
|GovernOnlyMainMemoryForProcesses|Bool, o padrão é FALSO|Estático|O comportamento padrão da Governança de Recursos é colocar limite especificado no MemoryInMB na quantidade de memória total (RAM + swap) que o processo utiliza. Se o limite for ultrapassado; o processo receberá exceção OutOfMemory. Se este parâmetro estiver definido para ser verdade; o limite será aplicado apenas à quantidade de memória RAM que um processo irá utilizar. Se este limite for ultrapassado; e se esta definição for verdadeira; em seguida, o OS trocará a memória principal para o disco. |
|IPProviderEnabled|Bool, o padrão é FALSO|Estático|Permite a gestão de endereços IP. |
|IsDefaultContainerRepositoryPasswordEncrypted|Bool, o padrão é FALSO|Estático|Se o PredevidoRepositoryPassword está encriptado ou não.|
|LinuxExternalExecutablePath|cadeia, padrão é "/usr/bin/" |Estático|O principal diretório de comandos executáveis externos no nó.|
|NTLMAuthenticationEnabled|Bool, o padrão é FALSO|Estático| Permite o suporte para a utilização do NTLM pelos pacotes de código que estão a funcionar como outros utilizadores para que os processos através das máquinas possam comunicar de forma segura. |
|NTLMAuthenticationPasswordSecret|SecureString, predefinido é comum:SecureString(")|Estático|É um tem encriptado que é usado para gerar a palavra-passe para utilizadores NTLM. Tem de ser definido se a NTLMAuthenticationEnabled for verdadeira. Validado pelo desdobrador. |
|NTLMSecurityUsersByX509CommonNamesRefreshInterval|TimeSpan, o padrão é comum:TimeSpan::FromMinutes(3)|Dinâmica|Especifique a hora de tempo em segundos. Definições específicas do ambiente O intervalo periódico em que o alojamento procura novos certificados a utilizar para a configuração NTLM do FileStoreService. |
|NTLMSecurityUsersByX509CommonNamesRefreshTimeout|TimeSpan, o padrão é comum:TimeSpan::FromMinutes(4)|Dinâmica| Especifique a hora de tempo em segundos. O prazo para configurar os utilizadores ntlm usando nomes comuns do certificado. Os utilizadores ntLM são necessários para as ações do FileStoreService. |
|PruneContainerImages|Bool, o padrão é FALSO|Dinâmica| Remova as imagens não utilizadas do recipiente de aplicação dos nódosos. Quando um ApplicationType não estiver registado no cluster Service Fabric, as imagens do recipiente que foram utilizadas por esta aplicação serão removidas em nós onde foi descarregado pelo Service Fabric. A poda funciona a cada hora, pelo que pode demorar até uma hora (mais tempo para podar a imagem) para que as imagens sejam removidas do cluster.<br>O Serviço Tecido nunca irá descarregar ou remover imagens não relacionadas com uma aplicação.  As imagens não relacionadas que foram descarregadas manualmente ou de outra forma devem ser removidas explicitamente.<br>As imagens que não devem ser eliminadas podem ser especificadas no parâmetro ContainerImagesToSkip.| 
|RegisterCodePackageHostTimeout|TimeSpan, o padrão é comum:TimeSpan:FromSeconds (120)|Dinâmica| Especifique a hora de tempo em segundos. O valor de tempo de tempo para a chamada de sincronização Do FabricRegisterCodePackageHost. Isto é aplicável apenas para anfitriões de aplicações de pacotes multicódigos como o FWP |
|Tempo de solicitação|TimeSpan, o padrão é comum:TimeSpan:FromSeconds (30)|Dinâmica| Especifique a hora de tempo em segundos. Isto representa o prazo de comunicação entre o anfitrião da aplicação do utilizador e o processo de Tecido para várias operações relacionadas com o alojamento, como o registo de fábrica; registo de tempo de execução. |
|RunAsPolicyEnabled| Bool, o padrão é FALSO|Estático| Permite a execução de pacotes de código como utilizador local, para além do utilizador em que o processo de tecido está a decorrer. Para ativar esta política, o Tecido deve estar a funcionar como SYSTEM ou como utilizador que tenha SeAssignPrimaryTokenPrivilege. |
|ServiceFactoryRegistrationTimeout| TimeSpan, o padrão é comum:TimeSpan:FromSeconds (120)|Dinâmica|Especifique a hora de tempo em segundos. O valor do tempo de paragem para o registo de sincronização (Apátrida/Apátrida)Chamada de Fábrica de Serviços |
|ServiceTypeDisableFailureThreshold |Número inteiro, padrão é 1 |Dinâmica|Este é o limiar para a contagem de falhas após a qual o FailoverManager (FM) é notificado para desativar o tipo de serviço nesse nó e tentar um nó diferente para colocação. |
|ServiceTypeDisableGraceInterval|TimeSpan, o padrão é comum:TimeSpan:FromSeconds (30)|Dinâmica|Especifique a hora de tempo em segundos. Intervalo de tempo após o qual o tipo de serviço pode ser desativado |
|ServiceTypeRegistrationTimeout |Tempo em Segundos, padrão é 300 |Dinâmica|Tempo máximo permitido para que o ServiceType seja registado com tecido |
|UseContainerServiceArguments|Bool, o padrão é VERDADE|Estático|Este config diz ao anfitrião para não passar argumentos (especificados em config ContainerServiceArguments) para docker daemon.|

## <a name="httpgateway"></a>HttpGateway

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|Ouvintes Ativos |Uint, o padrão é 50 |Estático| Número de leituras para publicar na fila do servidor http. Isto controla o número de pedidos simultâneos que podem ser satisfeitos pelo HttpGateway. |
|HttpGatewayHealthReportSendInterval |Tempo em segundos, padrão é 30 |Estático|Especifique a hora de tempo em segundos. O intervalo em que o Http Gateway envia relatórios de saúde acumulados ao Health Manager. |
|HttpStrictTransportSecurityHeader|string,padrão é ""|Dinâmica| Especifique o valor do cabeçalho http strict transport security a incluir em todas as respostas enviadas pelo HttpGateway. Quando definido para a corda vazia; este cabeçalho não será incluído na resposta do portal.|
|IsEnabled|Bool, o padrão é falso. |Estático| Ativa/desativa o HttpGateway. HttpGateway é desativado por padrão. |
|Tamanho MaxEntityBodySize |Uint, o padrão é 4194304 |Dinâmica|Dá o tamanho máximo do corpo que se pode esperar a partir de um pedido de http. O valor predefinido é de 4MB. Httpgateway falhará um pedido se tiver um corpo de tamanho > este valor. O tamanho mínimo de pedaço de leitura é 4096 bytes. Portanto, isto tem de ser >= 4096. |

## <a name="imagestoreservice"></a>ImageStoreService

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|Ativado |Bool, o padrão é falso. |Estático|A bandeira ativada para ImageStoreService. Padrão: falso |
|MinReplicaSetSize | Int, padrão é 3 |Estático|O MinReplicaSetSize para ImageStoreService. |
|PlacementConstraints | cadeia, padrão é "" |Estático| As Restrições de Colocação para ImageStoreService. |
|QuorumLossWaitDuration | Tempo em segundos, padrão é MaxValue |Estático| Especifique a hora de tempo em segundos. O QuorumLossWaitDura para ImageStoreService. |
|ReplicaRestartWaitDuration | Tempo em segundos, padrão é 60.0 \* 30 |Estático|Especifique a hora de tempo em segundos. A Duração da Reprodução Reinício de Duração do ImageStoreService. |
|StandByReplicaKeepDuration | Tempo em segundos, padrão é 3600.0 \* 2 |Estático| Especifique a hora de tempo em segundos. O StandByReplicaKeepDura para ImageStoreService. |
|TargetReplicaSetSize | int, padrão é 7 |Estático|O TargetReplicaSetSize para ImageStoreService. |

## <a name="ktllogger"></a>KtlLogger

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|AutomaticMemoryConfiguration |Int, padrão é 1 |Dinâmica|Sinalizar que indica se as definições de memória devem ser configuradas automaticamente e dinamicamente. Se zero, as definições de configuração da memória são utilizadas diretamente e não se alteram com base nas condições do sistema. Se uma, então as definições de memória forem configuradas automaticamente e podem mudar com base nas condições do sistema. |
|MaximumDestagingWriteOutstandingInKB | int, padrão é 0 |Dinâmica|O número de KB para permitir que o registo partilhado avance antes do registo dedicado. Use 0 para não indicar limite.
|SharedLogId |cadeia, padrão é "" |Estático|Guia único para recipiente de log partilhado. Utilize "" se utilizar o caminho predefinido sob a raiz de dados do tecido. |
|SharedLogPath |cadeia, padrão é "" |Estático|Nome do caminho e do arquivo para o local para colocar um recipiente de registo partilhado. Utilize "" para utilizar o caminho predefinido sob a raiz de dados do tecido. |
|SharedLogSizeInMB |Int, padrão é 8192 |Estático|O número de MB a locar no recipiente de registo partilhado. |
|SharedLogThrottleLimitInPercentUsed|int, padrão é 0 | Estático | A percentagem de utilização do tronco partilhado que induza estrangulamento. O valor deve ser entre 0 e 100. Um valor de 0 implica a utilização do valor percentual padrão. Um valor de 100 implica nenhuma aceleração. Um valor entre 1 e 99 especifica a percentagem de utilização de registo acima da qual o estrangulamento ocorrerá; por exemplo, se o registo partilhado for de 10GB e o valor for de 90, oestrangulamento ocorrerá uma vez que 9GB esteja em uso. Recomenda-se a utilização do valor predefinido.|
|WriteBufferMemoryPoolMaximumInKB | int, padrão é 0 |Dinâmica|O número de KB para permitir que o conjunto de memória tampão de escrita cresça. Use 0 para não indicar limite. |
|WriteBufferMemoryPoolMinimumInKB |Int, padrão é 8388608 |Dinâmica|O número de KB para inicialmente atribuir para o conjunto de memória tampão de escrita. Use 0 para indicar que o padrão sem limite deve ser consistente com SharedLogSizeInMB abaixo. |

## <a name="managedidentitytokenservice"></a>Serviço De Identidade Gerida
| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|IsEnabled|Bool, o padrão é FALSO|Estático|Flag controlando a presença e o estado do Serviço de Fichas de Identidade Gerida no cluster;este é um pré-requisito para a utilização da funcionalidade de identidade gerida das aplicações do Tecido de Serviço.|

## <a name="management"></a>Gestão

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|AutomaticUnprovisionInterval|TimeSpan, o padrão é comum:TimeSpan::FromMinutes (5)|Dinâmica|Especifique a hora de tempo em segundos. O intervalo de limpeza para o não registo do tipo de aplicação durante a limpeza automática do tipo de aplicação.|
|AzureStorageMaxConnections | Int, padrão é 5000 |Dinâmica|O número máximo de ligações simultâneas ao armazenamento em azul. |
|AzureStorageMaxWorkerThreads | int, padrão é 25 |Dinâmica|O número máximo de fios de trabalhador em paralelo. |
|AzureStorageOperationTimeout | Tempo em segundos, padrão é 6000 |Dinâmica|Especifique a hora de tempo em segundos. Tempo para a operação da xstore para completar. |
|CleanupApplicationPackageOnProvisionSuccess|Bool, o padrão é FALSO |Dinâmica|Permite ou desativa a limpeza automática do pacote de aplicações na provisão bem sucedida. |
|Tipos de aplicações não utilizados|Bool, o padrão é FALSO |Dinâmica|Esta configuração, se ativada, permite registar automaticamente versões do tipo de aplicação não utilizadas, saltando as três versões não utilizadas mais recentes, aparando assim o espaço do disco ocupado pela loja de imagens. A limpeza automática será desencadeada no final da provisão bem sucedida para esse tipo de aplicação específico e também funciona periodicamente uma vez por dia para todos os tipos de aplicações. O número de versões não utilizadas para saltar é configurável utilizando o parâmetro "MaxUnusedAppVersionsToKeep". |
|DisableChecksumValidation | Bool, o padrão é falso. |Estático| Esta configuração permite-nos ativar ou desativar a validação de verificação durante o fornecimento de aplicações. |
|DisableServerSideCopy | Bool, o padrão é falso. |Estático|Esta configuração permite ou desativa a cópia do pacote do lado do servidor da aplicação na ImageStore durante o fornecimento da aplicação. |
|ImageCachingEnabled | Bool, o padrão é verdade |Estático|Esta configuração permite-nos ativar ou desativar o cache. |
|ImageStoreConnectionString |SecureString |Estático|Fio de ligação à Raiz para ImageStore. |
|ImageStoreMinimumTransferBPS | Int, padrão é 1024 |Dinâmica|A taxa mínima de transferência entre o cluster e a ImageStore. Este valor é utilizado para determinar o tempo de paragem ao aceder à ImageStore externa. Altere este valor apenas se a latência entre o cluster e a ImageStore for elevada para permitir que o cluster descarregue a partir da ImageStore externa. |
|MaxUnusedAppTypeVersionsToKeep | Int, padrão é 3 |Dinâmica|Esta configuração define o número de versões do tipo de aplicação não utilizadas a serem ignoradas para limpeza. Este parâmetro só é aplicável se estiver ativado um parâmetro CleanupUnusedApplicationTypes. |


## <a name="metricactivitythresholds"></a>MetricActivityThresholds
| **Parâmetro** | **Valores Permitidos** |**Política de Upgrade**| **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|PropertyGroup|KeyIntegerValueMap, padrão é Nenhum|Dinâmica|Determina o conjunto de MetricActivityThresholds para as métricas do cluster. O equilíbrio funcionará se o maxNodeLoad for maior do que os MetricActivityThresholds. Para métricas de desfragiras define a quantidade de carga igual ou abaixo da qual o Tecido de Serviço considerará o nó vazio |

## <a name="metricbalancingthresholds"></a>MetricBalancingThresholds
| **Parâmetro** | **Valores Permitidos** |**Política de Upgrade**| **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, padrão é Nenhum|Dinâmica|Determina o conjunto de MetricBalancingThresholds para as métricas do cluster. O equilíbrio funcionará se maxNodeLoad/minNodeLoad for maior do que metricBalancingThresholds. A desfragmentação funcionará se maxNodeLoad/minNodeLoad em pelo menos um FD ou UD for menor que o MetricBalancingThresholds. |

## <a name="metricloadstickinessforswap"></a>MetricLoadStickinessForSwap
| **Parâmetro** | **Valores Permitidos** |**Política de Upgrade**| **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, padrão é Nenhum|Dinâmica|Determina a parte da carga que fica com réplica quando trocada Leva valor entre 0 (a carga não fica com réplica) e 1 (varas de carga com réplica - padrão) |

## <a name="namingservice"></a>NamingService

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|GatewayServiceDescriptionCacheLimit |int, padrão é 0 |Estático|O número máximo de entradas mantidas na cache de descrição do serviço LRU no Gateway naming (definido para 0 sem limite). |
|MaxClientConnections |Int, padrão é 1000 |Dinâmica|O número máximo permitido de ligações de clientes por gateway. |
|MaxFileOperationTimeout |Tempo em segundos, padrão é 30 |Dinâmica|Especifique a hora de tempo em segundos. O prazo máximo permitido para o funcionamento do serviço de loja de ficheiros. Os pedidos que especificam um prazo maior serão rejeitados. |
|MaxIndexedEmptyPartitions |Int, padrão é 1000 |Dinâmica|O número máximo de divisórias vazias que permanecerão indexadas na cache de notificação para sincronizar os clientes de religação. Quaisquer divisórias vazias acima deste número serão removidas do índice na ordem de versão de procura ascendente. Os clientes reconectarem-se ainda podem sincronizar e receber atualizações de partição vazias perdidas; mas o protocolo de sincronização torna-se mais caro. |
|MaxMessageSize |Int, o padrão é 4\*1024\*1024 |Estático|O tamanho máximo da mensagem para a comunicação do nó do cliente ao usar o nome. Alívio de ataque DOS; o valor predefinido é de 4MB. |
|MaxNamingServiceHealthReports | Int, padrão é 10 |Dinâmica|O número máximo de operações lentas que o serviço de loja Naming reporta não ser saudável de uma só vez. Se 0; todas as operações lentas são enviadas. |
|MaxOperationTimeout |Tempo em segundos, padrão é 600 |Dinâmica|Especifique a hora de tempo em segundos. O tempo máximo permitido para operações de clientes. Os pedidos que especificam um prazo maior serão rejeitados. |
|MaxOutstandingNotificationsPerClient |Int, padrão é 1000 |Dinâmica|O número máximo de notificações pendentes antes do registo de um cliente é fechado à força pelo portal. |
|MinReplicaSetSize | Int, padrão é 3 |Não é permitido| O número mínimo de réplicas do Serviço de Nomeação necessárias para escrever para completar uma atualização. Se houver menos réplicas do que esta ativa no sistema, o Sistema de Fiabilidade nega atualizações à Loja de Serviços de Nomeação até que as réplicas sejam restauradas. Este valor nunca deve ser mais do que o TargetReplicaSetSize. |
|PartitionCount |Int, padrão é 3 |Não é permitido|O número de divisórias da loja de Serviço sinuoso a criar. Cada partição possui uma única chave de partição que corresponde ao seu índice; assim, chaves de partição [0; PartitionCount] existem. O aumento do número de divisórias do Serviço de Nomeação aumenta a escala que o Serviço de Nomeação pode realizar diminuindo a quantidade média de dados detidos por qualquer conjunto de réplicas de apoio; a um custo de utilização acrescida de recursos (uma vez que as réplicas do serviço PartitionCount*ReplicaSetSize devem ser mantidas).|
|PlacementConstraints | cadeia, padrão é "" |Não é permitido| Restrição de colocação para o Serviço de Nomeação. |
|QuorumLossWaitDuration | Tempo em segundos, padrão é MaxValue |Não é permitido| Especifique a hora de tempo em segundos. Quando um Serviço de Nomeação entra em perda de quórum; este temporizador começa. Quando expirar, o FM considerará as réplicas para baixo como perdidas; e tentar recuperar quórum. Não que isto possa resultar em perda de dados. |
|Intervalo de Reparação | Tempo em segundos, padrão é 5 |Estático| Especifique a hora de tempo em segundos. Intervalo em que iniciará a reparação da inconsistência do nome entre o proprietário da autoridade e o proprietário do nome. |
|ReplicaRestartWaitDuration | Tempo em segundos, predefinido é (60.0 * 30)|Não é permitido| Especifique a hora de tempo em segundos. Quando uma réplica do Serviço de Nomeação cai; este temporizador começa. Quando expirar, o FM começará a substituir as réplicas que estão em baixo (ainda não as considera perdidas). |
|ServiceDescriptionCacheLimit | int, padrão é 0 |Estático| O número máximo de entradas mantidas na cache de descrição do serviço LRU no Serviço de Loja de Naming (definido para 0 sem limite). |
|ServiceNotificationTimeout |Tempo em segundos, padrão é 30 |Dinâmica|Especifique a hora de tempo em segundos. O tempo de tempo utilizado na entrega de notificações de serviço ao cliente. |
|StandByReplicaKeepDuration | Tempo em segundos, padrão é 3600.0 * 2 |Não é permitido| Especifique a hora de tempo em segundos. Quando uma réplica do Serviço de Nomeação voltar de um estado de baixo; pode já ter sido substituído. Este temporizador determina quanto tempo o FM manterá a réplica de espera antes de a descartar. |
|TargetReplicaSetSize |int, padrão é 7 |Não é permitido|O número de conjuntos de réplicas para cada partição da loja de serviço sinuoso. Aumentar o número de conjuntos de réplicas aumenta o nível de fiabilidade para a informação na Loja de Serviços de Nomeação; diminuir a alteração de que a informação será perdida em consequência de falhas no nó; com um custo de maior carga no Windows Fabric e o tempo que leva para realizar atualizações aos dados de nomeação.|

## <a name="nodebufferpercentage"></a>NodeBufferPercentage
| **Parâmetro** | **Valores Permitidos** |**Política de Upgrade**| **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, padrão é Nenhum|Dinâmica|Percentagem de capacidade do nó por nome métrico; usado como tampão para manter algum lugar livre em um nó para o caso failover. |

## <a name="nodecapacities"></a>NodeCapacities

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|PropertyGroup |NodeCapacityCollectionMap |Estático|Uma coleção de capacidades de nó para métricas diferentes. |

## <a name="nodedomainids"></a>NodeDomainIds

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|PropertyGroup |NodeFaultDomainIdCollection |Estático|Descreve os domínios de falha a que um nó pertence. O domínio da falha é definido através de um URI que descreve a localização do nó no datacenter.  Os URIs do Domínio da Falha são do formato fd:/fd/ seguidos por um segmento de percurso URI.|
|UpgradeDomainId |cadeia, padrão é "" |Estático|Descreve o domínio de upgrade a que um nó pertence. |

## <a name="nodeproperties"></a>NodeProperties

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|PropertyGroup |NodePropertyCollectionMap |Estático|Uma coleção de pares de valor-chave de corda para propriedades de nó. |

## <a name="paas"></a>Paas

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|ClusterId |cadeia, padrão é "" |Não é permitido|Loja de certificados X509 utilizada por tecido para proteção de configuração. |

## <a name="performancecounterlocalstore"></a>PerformanceCounterLocalStore

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|Balcões |String | Dinâmica |Lista separada da vírposta de contadores de desempenho para recolher. |
|IsEnabled |Bool, o padrão é verdade | Dinâmica |A bandeira indica se a recolha do contador de desempenho no nó local está ativada. |
|MaxCounterBinaryFileSizeInMB |Int, padrão é 1 | Dinâmica |Tamanho máximo (em MB) para cada ficheiro binário do contador de desempenho. |
|NewCounterBinaryFileCreationIntervalInMinutes |Int, padrão é 10 | Dinâmica |Intervalo máximo (em segundos) após o qual é criado um novo ficheiro binário de contra-desempenho. |
|SamplingIntervalInSeconds |Int, padrão é 60 | Dinâmica |Intervalo de amostragem para contadores de desempenho a ser recolhidos. |

## <a name="placementandloadbalancing"></a>PlacementAndLoadBalancing

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|AffinityConstraintPriority | int, padrão é 0 | Dinâmica|Determina a prioridade da restrição de afinidade: 0: Difícil; 1: Macio; negativo: Ignorar. |
|ApplicationCapacityConstraintPriority | int, padrão é 0 | Dinâmica|Determina a prioridade da restrição de capacidade: 0: Difícil; 1: Macio; negativo: Ignorar. |
|AutoDetectAvailableResources|Bool, o padrão é VERDADE|Estático|Esta config irá desencadear a deteção automática dos recursos disponíveis no nó (CPU e Memória) Quando este config for definido como verdadeiro - vamos ler capacidades reais e corrigi-las se o utilizador especificar as capacidades de mau nó ou não os definir de todo Se este config está definido para falso - nós vamos  Traçar um aviso de que o utilizador especificou as capacidades do nó mau; mas não vamos corrigi-los; o que significa que o utilizador quer ter as capacidades especificadas como > do que o nó realmente tem ou se as capacidades estão indefinidas; vai assumir capacidade ilimitada |
|BalancingDelayAfterNewNode | Tempo em segundos, padrão é 120 |Dinâmica|Especifique a hora de tempo em segundos. Não comece a equilibrar as atividades neste período após a adição de um novo nó. |
|BalancingDelayAfterNodeDown | Tempo em segundos, padrão é 120 |Dinâmica|Especifique a hora de tempo em segundos. Não comece a equilibrar as atividades neste período após um evento de descida de nós. |
|CapacidadeRestrição | int, padrão é 0 | Dinâmica|Determina a prioridade da restrição de capacidade: 0: Difícil; 1: Macio; negativo: Ignorar. |
|ConsecutiveDroppedMovementsHealthReportLimit | int, padrão é 20 | Dinâmica|Define o número de vezes consecutivas em que os Movimentos emitidos pelo ResourceBalancer são eliminados antes de serem realizados os diagnósticos e emitidos avisos sanitários. Negativo: Sem avisos emitidos sob esta condição. |
|ConstraintFixPartialDelayAfterNewNode | Tempo em segundos, padrão é 120 |Dinâmica| Especifique a hora de tempo em segundos. DDo não corrigir falhasDomain e upgradeViolações de restrições de domínio dentro deste período após a adição de um novo nó. |
|ConstraintFixPartialDelayAfterNodeDown | Tempo em segundos, padrão é 120 |Dinâmica| Especifique a hora de tempo em segundos. Não corrija as violações de restrição de Domínio de Falha e upgrade dentro deste período após um evento de descida de nós. |
|ConstraintViolationHealthReportLimit | int, padrão é 50 |Dinâmica| Define o número de vezes que a violação da réplica tem de ser persistentemente desfixada antes de os diagnósticos serem realizados e os relatórios de saúde serem emitidos. |
|DetailedConstraintViolationHealthReportLimit | Int, padrão é 200 |Dinâmica| Define o número de vezes que a réplica de violação de restrição tem de ser persistentemente desfixada antes de serem realizados diagnósticos e são emitidos relatórios de saúde detalhados. |
|DetailedDiagnosticsInfoListLimit | int, padrão é 15 |Dinâmica| Define o número de entradas de diagnóstico (com informações detalhadas) por restrição a incluir antes da truncação em Diagnósticos.|
|DetailedNodeListLimit | int, padrão é 15 |Dinâmica| Define o número de nós por restrição a incluir antes da truncação nos relatórios de Réplica não colocadas. |
|DetailedPartitionListLimit | int, padrão é 15 |Dinâmica| Define o número de divisórias por entrada de diagnóstico para uma restrição a incluir antes da truncação em Diagnósticos. |
|DetailedVerboseHealthReportLimit | Int, padrão é 200 | Dinâmica|Define o número de vezes que uma réplica não colocada tem de ser persistentemente descolocada antes de serem emitidos relatórios de saúde detalhados. |
|Capacidades métricas do Serviço de Aplicação|Bool, o padrão é FALSO | Estático |Permite a proteção de serviços de tecido. Todos os serviços de utilizador estão sob um único objeto de trabalho/cgroup e limitados à quantidade especificada de recursos. Isto tem de ser estático (requer o reinício do FabricHost) como criação/remoção do objeto de trabalho do utilizador e definição de limites em feito durante a abertura do Hospedeiro de Tecido. |
|FaultDomainConstraintPriority | int, padrão é 0 |Dinâmica| Determina a prioridade da restrição de domínio de avaria: 0: Difícil; 1: Macio; negativo: Ignorar. |
|GlobalMovementThrottleCountingInterval | Tempo em segundos, padrão é 600 |Estático| Especifique a hora de tempo em segundos. Indicar o comprimento do intervalo passado para o qual rastrear os movimentos de réplica de domínio (utilizadojuntamente com o GlobalMovementThrottleThreshold). Pode ser definido para 0 para ignorar completamente a aceleração global. |
|GlobalMovementThrottleThreshold | Uint, o padrão é 1000 |Dinâmica| Número máximo de movimentos permitidos na Fase de Equilíbrio no intervalo anterior indicado pelo GlobalMovementThrottleCountingInterval. |
|GlobalMovementThrottleThresholdForBalancing | uint, padrão é 0 | Dinâmica|Número máximo de movimentos permitidos na Fase de Equilíbrio no intervalo anterior indicado pelo GlobalMovementThrottleCountingInterval. 0 indica que não há limite. |
|GlobalMovementThrottleThresholdForPlacement | uint, padrão é 0 |Dinâmica| O número máximo de movimentos permitidos na Fase de Colocação no intervalo anterior indicado pelo GlobalMovementThrottleCountingInterval.0 não indica limite.|
|GlobalMovementThrottleThresholdPercentage|duplo, padrão é 0|Dinâmica|Número máximo de movimentos totais permitidos nas fases de Equilíbrio e Colocação (expressoem em percentagem do número total de réplicas no cluster) no intervalo anterior indicado pelo GlobalMovementThrottleCountingInterval. 0 indica que não há limite. Se este e o GlobalMovementThrottleThreshold forem especificados; então é usado um limite mais conservador.|
|GlobalMovementThrottleThresholdPercentageForBalancing|duplo, padrão é 0|Dinâmica|Número máximo de movimentos permitidos na Fase de Equilíbrio (expresso em percentagem do número total de réplicas em PLB) no intervalo anterior indicado pelo GlobalMovementThrottleCountingInterval. 0 indica que não há limite. Se for em especificar este e o GlobalMovementThrottleThresholdForBalance; então é usado um limite mais conservador.|
|InBuildThrottlingAssociatedMetric | cadeia, padrão é "" |Estático| O nome métrico associado para esta estrangulamento. |
|InBuildThrottlingEnabled | Bool, o padrão é falso. |Dinâmica| Determine se a aceleração em construção está ativada. |
|InBuildThrottlingGlobalMaxValue | int, padrão é 0 |Dinâmica|O número máximo de réplicas em construção permitida globalmente. |
|InterruptBalancingForAllFailoverUnitUpdates | Bool, o padrão é falso. | Dinâmica|Determina se qualquer tipo de atualização da unidade failover deve interromper o equilíbrio rápido ou lento. Com um balanço "falso" especificado será interrompido se failoverUnit: for criado/eliminado; tem réplicas em falta; mudou a localização da réplica primária ou mudou o número de réplicas. O balanço não será interrompido noutros casos - se failoverUnit: tiver réplicas extra; mudou qualquer bandeira de réplica; alterada apenas a versão de partição ou qualquer outro caso. |
|MinConstraintCheckInterval | Tempo em segundos, padrão é 1 |Dinâmica| Especifique a hora de tempo em segundos. Define o tempo mínimo que deve passar antes de duas rondas de verificação de restrições consecutivas. |
|MinLoadBalancingInterval | Tempo em segundos, padrão é 5 |Dinâmica| Especifique a hora de tempo em segundos. Define o tempo mínimo que deve passar antes de duas rondas de equilíbrio consecutivas. |
|MinPlacementInterval | Tempo em segundos, padrão é 1 |Dinâmica| Especifique a hora de tempo em segundos. Define o tempo mínimo que deve passar antes de duas rondas de colocação consecutivas. |
|MoveExistingReplicaForPlacement | Bool, o padrão é verdade |Dinâmica|Definição que determina se mover a réplica existente durante a colocação. |
|MovementPerPartitionThrottleCountingInterval | Tempo em segundos, padrão é 600 |Estático| Especifique a hora de tempo em segundos. Indicar o comprimento do intervalo passado para o qual rastrear os movimentos da réplica para cada partição (utilizado juntamente com o MovementPerPartitionThrottleThreshold). |
|MovementPerPartitionThrottleThreshold | Uint, o padrão é 50 |Dinâmica| Não ocorrerá qualquer movimento relacionado com o equilíbrio para uma partilha se o número de movimentos relacionados de equilíbrio para réplicas dessa divisória tiver atingido ou ultrapassado o MovimentoPerFailoverUnitThrottleThreshold no intervalo anterior indicado por MovementPerPartitionThrottleCountingInterval. |
|MoveParentToFixAffinityViolation | Bool, o padrão é falso. |Dinâmica| Definição que determina se as réplicas dos pais podem ser movidas para corrigir restrições de afinidade.|
|PartiallyPlaceServices | Bool, o padrão é verdade |Dinâmica| Determina se todas as réplicas de serviço no cluster serão colocadas "tudo ou nada" dadas a nós limitados adequados para eles.|
|PlaceChildWithoutParent | Bool, o padrão é verdade | Dinâmica|Definição que determina se a réplica do serviço infantil pode ser colocada se não houver réplica dos pais. |
|PlacementConstraintPriority | int, padrão é 0 | Dinâmica|Determina a prioridade da restrição de colocação: 0: Difícil; 1: Macio; negativo: Ignorar. |
|PlacementConstraintValidationCacheSize | Int, padrão é 10000 |Dinâmica| Limita o tamanho da tabela utilizada para validação rápida e cache de Expressões de Restrição de Colocação. |
|PlacementSearchTimeout | Tempo em segundos, padrão é 0.5 |Dinâmica| Especifique a hora de tempo em segundos. Ao colocar serviços; procure no máximo este tempo antes de devolver um resultado. |
|PLBRefreshGap | Tempo em segundos, padrão é 1 |Dinâmica| Especifique a hora de tempo em segundos. Define o tempo mínimo que deve passar antes que plb refresca o estado novamente. |
|PreferredLocationConstraintPriority | Int, padrão é 2| Dinâmica|Determina a prioridade da restrição de localização preferida: 0: Difícil; 1: Macio; 2: Otimização; negativo: Ignorar |
|PreferUpgradedUDs|bool,padrão é FALSO|Dinâmica|Liga e desliga a lógica que prefere passar para UDs já atualizados. A partir de SF 7.0, o valor predefinido para este parâmetro é alterado de TRUE para FALSE.|
|PreventTransientOvercommit | Bool, o padrão é falso. | Dinâmica|Determina que a PLB conte imediatamente com recursos que serão libertados pelos movimentos iniciados. Por defeito; PlB pode iniciar a mudança e avançar no mesmo nó que pode criar um excesso transitório. A definição deste parâmetro como verdadeira evitará que este tipo de compromissos excessivos e desfragem a pedido (aka placementWithMove) serão desativados. |
|ScaleoutCountConstraintPriority | int, padrão é 0 |Dinâmica| Determina a prioridade da restrição de contagem de escala: 0: Dura; 1: Macio; negativo: Ignorar. |
|SwapPrimaryThrottlingAssociatedMetric | cadeia, padrão é ""|Estático| O nome métrico associado para esta estrangulamento. |
|SwapPrimaryThrottlingEnabled | Bool, o padrão é falso.|Dinâmica| Determine se a aceleração primária de troca está ativada. |
|SwapPrimaryThrottlingGlobalMaxValue | int, padrão é 0 |Dinâmica| O número máximo de réplicas primárias de swap permitidas globalmente. |
|TraceCRMReasons |Bool, o padrão é verdade |Dinâmica|Especifica se deve traçar razões para a CRM emitiu movimentos para o canal de eventos operacionais. |
|UpgradeDomainConstraintPriority | Int, padrão é 1| Dinâmica|Determina a prioridade da restrição de domínio de atualização: 0: Difícil; 1: Macio; negativo: Ignorar. |
|UseMoveCostReports | Bool, o padrão é falso. | Dinâmica|Instrui o LB a ignorar o elemento de custo da função de pontuação; resultando um número potencialmente grande de movimentos para uma colocação melhor equilibrada. |
|UseSeparateSecondaryLoad | Bool, o padrão é verdade | Dinâmica|Definição que determina se utilizar uma carga secundária diferente. |
|ValidatePlacementConstraint | Bool, o padrão é verdade |Dinâmica| Especifica se a expressão PlacementConstraint para um serviço é ou não validada quando o Serviço Descrição de um serviço é atualizado. |
|ValidaçãoPrimaryPlacementConstraintOnPromote| Bool, o padrão é VERDADE |Dinâmica|Especifica se a expressão PlacementConstraint para um serviço é avaliada para preferência primária em failover. |
|VerboseHealthReportLimit | int, padrão é 20 | Dinâmica|Define o número de vezes que uma réplica tem de passar descolocada antes de ser comunicado um aviso sanitário (se o relatório de saúde verboso estiver ativado). |
|NodeLoadsOperationalTracingEnabled | Bool, o padrão é verdade |Dinâmica|Config que permite vestígios estruturais operacionais no Nó Load na loja de eventos. |
|Intervalo de rastreio operacional nonótol | TimeSpan, o padrão é comum:TimeSpan:FromSeconds (20) | Dinâmica|Especifique a hora de tempo em segundos. O intervalo com o qual rastrear cargas de nó para loja de eventos para cada domínio de serviço. |

## <a name="reconfigurationagent"></a>ReconfigurationAgent

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|ApplicationUpgradeMaxReplicaCloseDuration | Tempo em segundos, padrão é 900 |Dinâmica|Especifique a hora de tempo em segundos. A duração pela qual o sistema irá esperar antes de encerrar os anfitriões de serviço que tenham réplicas que estejam presas de perto durante a Atualização da Aplicação.|
|FabricUpgradeMaxReplicaCloseDuration | Tempo em segundos, padrão é 900 |Dinâmica| Especifique a hora de tempo em segundos. A duração pela qual o sistema aguardará antes de encerrar os anfitriões de serviço que tenham réplicas que estejam presas durante a atualização do tecido. |
|GracefulReplicaShutdownMaxDuration|TimeSpan, o padrão é comum:TimeSpan:FromSeconds (120)|Dinâmica|Especifique a hora de tempo em segundos. A duração pela qual o sistema irá esperar antes de encerrar os anfitriões de serviço que têm réplicas que estão presas por perto. Se este valor estiver definido para 0, as réplicas não serão instruídas a fechar.|
|NodeDeactivationMaxReplicaCloseDuration | Tempo em segundos, padrão é 900 |Dinâmica|Especifique a hora de tempo em segundos. A duração pela qual o sistema aguardará antes de terminar os anfitriões de serviço que tenham réplicas que estejam presas perto durante a desativação do nó. |
|PeriodicApiSlowTraceInterval | Tempo em segundos, padrão é de 5 minutos |Dinâmica| Especifique a hora de tempo em segundos. PeriodicApiSlowTraceInterval define o intervalo sobre o qual as chamadas API lentas serão retraçadas pelo monitor API. |
|ReplicaChangeRoleFailureRestartThreshold|Int, padrão é 10|Dinâmica| Inteiro. Especifique o número de falhas de API durante a promoção primária após a qual será aplicada uma ação de auto-mitigação (reinício de réplica). |
|ReplicaChangeRoleFailureWarningReportThreshold|int, padrão é 2147483647|Dinâmica| Inteiro. Especifique o número de falhas da API durante a promoção primária após o qual será levantado um relatório de saúde de alerta.|
|ServiceApiHealthDuration | Tempo em segundos, padrão é de 30 minutos |Dinâmica| Especifique a hora de tempo em segundos. ServiceApiHealthDura define quanto tempo esperamos que um Serviço API seja executado antes de denunciá-lo insalubre. |
|ServiceReconfigurationApiHealthDuration | Tempo em segundos, padrão é 30 |Dinâmica| Especifique a hora de tempo em segundos. ServiçoRereconfiguraçãoApiHealthDura define quanto tempo esperamos que um Serviço API seja executado antes de reportarmos insalubre. Isto aplica-se às chamadas da API que têm impacto na disponibilidade.|

## <a name="replication"></a>Replicação
| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade**| **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|BatchAcknowledgementInterval|TimeSpan, o padrão é comum:TimeSpan::FromMilliseconds (15)|Estático|Especifique a hora de tempo em segundos. Determina a quantidade de tempo que o replicador espera depois de receber uma operação antes de enviar de volta um reconhecimento. Outras operações recebidas durante este período terão os seus agradecimentos enviados numa única mensagem-> reduzindo o tráfego de rede, mas potencialmente reduzindo a produção do replicador.|
|MaxCopyQueueSize|uint, padrão é 1024|Estático|Este é o valor máximo que define o tamanho inicial para a fila que mantém as operações de replicação. Note que deve ser uma potência de 2. Se durante o tempo de funcionamento a fila crescer para este tamanho, a operação será acelerada entre os replicadores primários e secundários.|
|MaxPrimaryReplicationQueueMemorySize|uint, padrão é 0|Estático|Este é o valor máximo da fila de replicação primária em bytes.|
|MaxPrimaryReplicationQueueSize|uint, padrão é 1024|Estático|Este é o número máximo de operações que podem existir na fila de replicação primária. Note que deve ser uma potência de 2.|
|MaxReplicationMessageSize|Uint, padrão é 52428800|Estático|Tamanho máximo da mensagem das operações de replicação. O padrão é de 50MB.|
|MaxSecondaryReplicationQueueMemorySize|uint, padrão é 0|Estático|Este é o valor máximo da fila de replicação secundária em bytes.|
|MaxSecondaryReplicationQueueSize|uint, padrão é 2048|Estático|Este é o número máximo de operações que podem existir na fila de replicação secundária. Note que deve ser uma potência de 2.|
|QueueHealthMonitoringInterval|TimeSpan, o padrão é comum:TimeSpan:FromSeconds (30)|Estático|Especifique a hora de tempo em segundos. Este valor determina o período de tempo utilizado pelo Replicador para monitorizar quaisquer eventos de saúde de aviso/erro nas filas de operação de replicação. Um valor de '0' desativa a monitorização da saúde |
|QueueHealthWarningAtUsagePercent|uint, padrão é 80|Estático|Este valor determina o uso da fila de replicação (em percentagem) após o que reportamos alertando sobre o elevado uso da fila. Fazemos isso depois de um intervalo de graça de QueueHealthMonitoringInterval. Se o uso da fila ficar abaixo desta percentagem no intervalo de graça|
|ReplicatorAddress|cadeia, padrão é "localhost:0"|Estático|O ponto final sob a forma de uma corda -'IP:Porta' que é utilizada pelo Replicador de Tecidos do Windows para estabelecer ligações com outras réplicas para enviar/receber operações.|
|ReplicatorListenAddress|cadeia, padrão é "localhost:0"|Estático|O ponto final em forma de corda -'IP:Porta' que é usado pelo Replicador de Tecido si para receber operações de outras réplicas.|
|ReplicatorPublishAddress|cadeia, padrão é "localhost:0"|Estático|O ponto final em forma de corda -'IP:Porta' que é usado pelo Replicador de Tecido si para enviar operações para outras réplicas.|
|Intervalo de retry|TimeSpan, o padrão é comum:TimeSpan::FromSeconds (5)|Estático|Especifique a hora de tempo em segundos. Quando uma operação é perdida ou rejeitada, este temporizador determina com que frequência o replicador irá voltar a tentar enviar a operação.|

## <a name="resourcemonitorservice"></a>ResourceMonitorService
| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade**| **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|IsEnabled|Bool, o padrão é FALSO |Estático|Controle se o serviço estiver ativado no cluster ou não. |

## <a name="runas"></a>Runas

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|RunAsAccountName |cadeia, padrão é "" |Dinâmica|Indica o nome da conta RunAs. Isto só é necessário para o tipo de conta "DomainUser" ou "ManagedServiceAccount". Os valores válidos são "domain\user" ou "user@domain". |
|RunAsAccountType|cadeia, padrão é "" |Dinâmica|Indica o tipo de conta RunAs. Isto é necessário para qualquer secção RunAs Valores válidos são "DomainUser/NetworkService/ManagedServiceAccount/LocalSystem".|
|RunAsPassword|cadeia, padrão é "" |Dinâmica|Indica a senha da conta RunAs. Isto só é necessário para o tipo de conta "DomainUser". |

## <a name="runas_dca"></a>RunAs_DCA

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|RunAsAccountName |cadeia, padrão é "" |Dinâmica|Indica o nome da conta RunAs. Isto só é necessário para o tipo de conta "DomainUser" ou "ManagedServiceAccount". Os valores válidos são "domain\user" ou "user@domain". |
|RunAsAccountType|cadeia, padrão é "" |Dinâmica|Indica o tipo de conta RunAs. Isto é necessário para qualquer secção RunAs Valores válidos são "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunAsPassword|cadeia, padrão é "" |Dinâmica|Indica a senha da conta RunAs. Isto só é necessário para o tipo de conta "DomainUser". |

## <a name="runas_fabric"></a>RunAs_Fabric

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|RunAsAccountName |cadeia, padrão é "" |Dinâmica|Indica o nome da conta RunAs. Isto só é necessário para o tipo de conta "DomainUser" ou "ManagedServiceAccount". Os valores válidos são "domain\user" ou "user@domain". |
|RunAsAccountType|cadeia, padrão é "" |Dinâmica|Indica o tipo de conta RunAs. Isto é necessário para qualquer secção RunAs Valores válidos são "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunAsPassword|cadeia, padrão é "" |Dinâmica|Indica a senha da conta RunAs. Isto só é necessário para o tipo de conta "DomainUser". |

## <a name="runas_httpgateway"></a>RunAs_HttpGateway

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|RunAsAccountName |cadeia, padrão é "" |Dinâmica|Indica o nome da conta RunAs. Isto só é necessário para o tipo de conta "DomainUser" ou "ManagedServiceAccount". Os valores válidos são "domain\user" ou "user@domain". |
|RunAsAccountType|cadeia, padrão é "" |Dinâmica|Indica o tipo de conta RunAs. Isto é necessário para qualquer secção RunAs Valores válidos são "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunAsPassword|cadeia, padrão é "" |Dinâmica|Indica a senha da conta RunAs. Isto só é necessário para o tipo de conta "DomainUser". |

## <a name="security"></a>Segurança
| **Parâmetro** | **Valores Permitidos** |**Política de Upgrade**| **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|AADCertEndpointFormat|cadeia, padrão é ""|Estático|Formato AAD Cert Endpoint, default Azure Commercial, especificado para ambiente não predefinido como o Governo azure "https:\//login.microsoftonline.us/{0}/federaçõesmetametais/2007-06/federationmetadata.xml" |
|AADClientApplication|cadeia, padrão é ""|Estático|Nome de pedido de cliente nativo ou ID representando clientes de tecido |
|AADClusterApplication|cadeia, padrão é ""|Estático|Nome ou ID da aplicação Web API que representa o cluster |
|AADLoginEndpoint|cadeia, padrão é ""|Estático|AAD Login Endpoint, default Azure Commercial, especificado para ambiente não predefinido como Governo Azure "https:\//login.microsoftonline.us" |
|AADTenantId|cadeia, padrão é ""|Estático|ID do inquilino (GUID) |
|AcceptExpiredPinnedClusterCertificate|Bool, o padrão é FALSO|Dinâmica|A bandeira indicando se aceita certificados de cluster expirados declarados por impressão digital aplica-se apenas aos certificados de cluster; de modo a manter o aglomerado vivo. |
|AdminClientCertThumbprints|cadeia, padrão é ""|Dinâmica|Impressões digitais de certificados utilizados pelos clientes em função de administrador. É uma lista de nomes separados de vírem. |
|AADTokenEndpointFormat|cadeia, padrão é ""|Estático|AAD Token Endpoint, default Azure Commercial, especificado para ambiente não predefinido como O Governo Azure "https:\//login.microsoftonline.us/{0}" |
|AdminClientClaims|cadeia, padrão é ""|Dinâmica|Todas as reclamações possíveis esperadas de clientes administradores; o mesmo formato que o ClienteClaims; esta lista é adicionada internamente às Reclamações de Clientes; por isso, não há necessidade de adicionar as mesmas entradas ao ClientClaims. |
|AdminClientIdentities|cadeia, padrão é ""|Dinâmica|Identidades windows de clientes de tecido em função de administrador; usado para autorizar operações de tecido privilegiado. É uma lista separada de vírpostas; cada entrada é um nome de conta de domínio ou nome de grupo. Por conveniência; a conta que executa o tecido.exe é automaticamente atribuída a função de administrador; assim como os administradores de serviço sinuoso. |
|AppRunAsAccountGroupX509Folder|cadeia, padrão é /casa/sfuser/sfusercerts |Estático|Pasta onde estão localizados certificados AppRunAsAccountGroup X509 e chaves privadas |
|CertificateExpirySafetyMargin|TimeSpan, o padrão é comum:TimeSpan:FromMinutes (43200)|Estático|Especifique a hora de tempo em segundos. Margem de segurança para a caducidade do certificado; o estado do relatório de saúde do certificado muda de OK para Aviso quando a expiração é mais próxima do que esta. O padrão é de 30 dias. |
|CertificateHealthReportingInterval|TimeSpan, o padrão é comum:TimeSpan::FromSeconds (3600 * 8)|Estático|Especifique a hora de tempo em segundos. Especificar intervalo para relatórios de saúde de certificado; padrão para 8 horas; definição para 0 relatórios de saúde de certificadode invalides |
|ClientCertThumbprints|cadeia, padrão é ""|Dinâmica|Impressões digitais de certificados utilizados pelos clientes para falar com o cluster; cluster usa esta ligação de entrada autorizada. É uma lista de nomes separados de vírem. |
|ClientClaimAuthEnabled|Bool, o padrão é FALSO|Estático|Indica se a autenticação baseada em reclamações está ativada nos clientes; definir este verdadeiro implicitamente define o ClientRoleEnabled. |
|ClientClaims|cadeia, padrão é ""|Dinâmica|Todas as reclamações possíveis esperadas dos clientes para a ligação ao gateway. Esta é uma lista de 'OR': ClaimsEntry \|\| ClaimsEntry \|\| ClaimsEntry ... cada ClaimEntry é uma lista "AND": ClaimType=ClaimValue && ClaimType=ClaimValue && ClaimType=ClaimValue ... |
|Identidades do Cliente|cadeia, padrão é ""|Dinâmica|Identidades windows do FabricClient; nomear gateway usa isto para autorizar ligações de entrada. É uma lista separada de vírpostas; cada entrada é um nome de conta de domínio ou nome de grupo. Por conveniência; a conta que executa o tecido.exe é automaticamente permitida; assim são os Utilizadores de Serviços de GrupoFabricAllowedE e ServiceFabricAdministrators. |
|ClientRoleEnabled|Bool, o padrão é FALSO|Estático|Indica se a função do cliente está ativada; quando definido para verdade; os clientes são atribuídos a papéis com base nas suas identidades. Para V2; permitir isto significa que o cliente que não está no AdminClientCommonNames/AdminClientIdentities só pode executar operações apenas de leitura. |
|ClusterCertThumbprints|cadeia, padrão é ""|Dinâmica|Impressões digitais de certificados autorizados a aderir ao cluster; uma lista de nomes separados de vírem. |
|ClusterCredentialType|cadeia, padrão é "Nenhum"|Não é permitido|Indica o tipo de credenciais de segurança a utilizar para proteger o cluster. Os valores válidos são "Nenhum/X509/Windows" |
|ClusterIdentities|cadeia, padrão é ""|Dinâmica|Identidades do windows dos nós de cluster; usado para autorização de adesão de cluster. É uma lista separada de vírpostas; cada entrada é um nome de conta de domínio ou nome de grupo |
|ClusterSpn|cadeia, padrão é ""|Não é permitido|Nome principal de serviço do cluster; quando o tecido funciona como um único utilizador de domínio (gMSA/conta de utilizador de domínio). É o SPN de ouvintes e ouvintes de arrendamento em fabric.exe: ouvintes da federação; ouvintes de replicação interna; ouvinte de serviço de execução e ouvinte de gateway nomeando. Isto deve ser deixado vazio quando o tecido funciona como contas de máquina; nesse caso, ligar o ouvinte do lado da computação SPN do endereço de transporte de ouvintes. |
|CrlCheckingFlag|uint, padrão é 0x40000000000|Dinâmica|Bandeira de validação de cadeia de certificados predefinido; Podem ser sobremontados por uma bandeira específica dos componentes; por exemplo, Federação/X509CertChainFlags 0x100000000CERT_CHAIN_REVOCATION_CHECK_END_CERT 0x200000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN 0x400000000CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT 0x8000000000CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY Definição para 0 desativar a VERIFICAção de CRL A lista completa dos valores suportados é documentada por dwFlags of CertGetCertificateChain: https://msdn.microsoft.com/library/windows/desktop/aa376078(v=vs.85).aspx |
|CrlDisablePeriod|TimeSpan, o padrão é comum:TimeSpan:FromMinutes(15)|Dinâmica|Especifique a hora de tempo em segundos. Quanto tempo a verificação de CRL é desativada para um determinado certificado depois de encontrar um erro offline; se o erro offline da CRL pode ser ignorado. |
|CrlOfflineHealthReportTtl|TimeSpan, o padrão é comum:TimeSpan:FromMinutes (1440)|Dinâmica|Especifique a hora de tempo em segundos. |
|DisableFirewallRuleForDomainProfile| Bool, o padrão é VERDADE |Estático| Indica se a regra da firewall não deve ser ativada para o perfil de domínio |
|DisableFirewallRuleForPrivateProfile| Bool, o padrão é VERDADE |Estático| Indica se a regra da firewall não deve ser ativada para o perfil privado | 
|DisableFirewallRuleForPublicProfile| Bool, o padrão é VERDADE | Estático|Indica se a regra da firewall não deve ser ativada para o perfil público |
| Versão EnforceLinuxMinTls | Bool, o padrão é FALSO | Estático | Se for definido como verdadeiro; apenas a versão TLS 1.2+ é suportada.  Se falso; suporte versões TLS anteriores. Aplica-se apenas ao Linux |
|FabricHostSpn| cadeia, padrão é "" |Estático| Nome principal de serviço de FabricHost; quando o tecido funciona como um único utilizador de domínio (gMSA/conta de utilizador de domínio) e o FabricHost é executado sob conta de máquina. É o SPN do iPC ouvinte para FabricHost; que por padrão deve ser deixado vazio uma vez que fabricHost corre sob conta de máquina |
|IgnoreCrlOfflineError|Bool, o padrão é FALSO|Dinâmica|Se ignorar erro offline da CRL quando o lado do servidor verifica os certificados de cliente que chegam |
|IgnoreSvrCrlOfflineError|Bool, o padrão é VERDADE|Dinâmica|Se ignorar o erro offline da CRL quando o lado do cliente verifica os certificados de servidor de entrada; padrão para verdade. Os ataques com certificados de servidor revogados requerem DNS comprometedores; mais difícil do que com certificados de cliente revogados. |
|ServerAuthCredentialType|cadeia, padrão é "Nenhum"|Estático|Indica o tipo de credenciais de segurança a utilizar para garantir a comunicação entre o FabricClient e o Cluster. Os valores válidos são "Nenhum/X509/Windows" |
|ServerCertThumbprints|cadeia, padrão é ""|Dinâmica|Impressões digitais de certificados de servidor usados pelo cluster para falar com os clientes; os clientes usam isto para autenticar o cluster. É uma lista de nomes separados de vírem. |
|SettingsX509StoreName| cadeia, padrão é "MY"| Dinâmica|Loja de certificados X509 utilizada por tecido para proteção de configuração |
|UseClusterCertForIpcServerTlsSecurity|Bool, o padrão é FALSO|Estático|Se usar certificado de cluster para proteger unidade de transporte IPC Server TLS |
|X509Pasta|cadeia, padrão é /var/lib/waagent|Estático|Pasta onde estão localizados certificados X509 e chaves privadas |
|TLS1_2_CipherList| string| Estático|Se for definido para uma corda não vazia; substitui a lista de cifrasuporta suportada para TLS1.2 e abaixo. Consulte a documentação "openssl-ciphers" para recuperar a lista de cifras apoiada e o formato de lista Exemplo de lista de cifra forte para TLS1.2: "ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA34: ECDHE-RSA-AES256-GCM-SHA34: ECDHE-RSA-AES256-GCM-SHA34: ECDHE-RSA-AES256-GCM-SHA34: ECDHE-RSA-AES256-GCM-SHA34: ECDHE-RSA-AES256-GCM-SHA34: ECDHE-RSA-AES256-GCM-SHA34: ECDHE-RSA-AES256-GCM-SHA34: ECDHE-RSA-AES256-GCM-SHA348: ECDHE-RSA-AES256-GCM-SHA34: ECDHE-RSA-AES256-GCM-SHA34: ECDHE-RSA ECDHE-RSA-AES-128-GCM-SHA256:ECDHE-ECDSA-AES256-CBC-SHA384:ECDHE-ECDSA-AES128-CBC-SHA256:ECDHE-RSA-AES256-CBC-SHA384:ECDHE-RSA-AES128-CBC-SHA256" Aplica-se apenas ao Linux. |

## <a name="securityadminclientx509names"></a>Security/AdminClientX509Names

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, padrão é Nenhum|Dinâmica|Esta é uma lista de pares "Nome" e "Valor". Cada "Nome" é de nome comum ou DnsName de certificados X509 autorizados para operações de clientes administrativos. Para um dado "Nome", "Valor" é uma lista separada de impressões digitais de certificado para emitente, se não vazia, o emitente direto dos certificados de cliente administrativo deve estar na lista. |

## <a name="securityclientaccess"></a>Segurança/Acesso ao Cliente

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|ActivateNode |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para ativação de um nó. |
|CancelTestCommand |cadeia, padrão é "Administrador" |Dinâmica| Cancela um TestCommand específico - se estiver em voo. |
|CodePackageControl |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para reiniciar pacotes de código. |
|CreateApplication |cadeia, padrão é "Administrador" | Dinâmica|Configuração de segurança para criação de aplicações. |
|CreateComposeDeployment|cadeia, padrão é "Administrador"| Dinâmica|Cria uma implementação de composição descrita por ficheiros de composição |
|CreateGatewayResource|cadeia, padrão é "Administrador"| Dinâmica|Criar um recurso de gateway |
|CreateName |cadeia, padrão é "Administrador" |Dinâmica|Configuração de segurança para nomear a criação URI. |
|CreateNetwork|cadeia, padrão é "Administrador" |Dinâmica|Cria uma rede de contentores |
|CreateService |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para criação de serviço. |
|CreateServiceFromTemplate |cadeia, padrão é "Administrador" |Dinâmica|Configuração de segurança para criação de serviço a partir do modelo. |
|CreateVolume|cadeia, padrão é "Administrador"|Dinâmica|Cria um volume |
|DeactivateNode |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para desativar um nó. |
|DeactivateNodesBatch |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para desativar vários nódosos. |
|Eliminar |cadeia, padrão é "Administrador" |Dinâmica| Configurações de segurança para o cliente da loja de imagens apagar operação. |
|Excluir Aplicação |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para eliminação da aplicação. |
|DeleteComposeDeployment|cadeia, padrão é "Administrador"| Dinâmica|Elimina a implantação de composição |
|DeleteGatewayResource|cadeia, padrão é "Administrador"| Dinâmica|Elimina um recurso de gateway |
|DeleteName |cadeia, padrão é "Administrador" |Dinâmica|Configuração de segurança para nomear a eliminação do URI. |
|DeleteNetwork|cadeia, padrão é "Administrador" |Dinâmica|Elimina uma rede de contentores |
|Eliminar Serviço |cadeia, padrão é "Administrador" |Dinâmica|Configuração de segurança para eliminação do serviço. |
|DeleteVolume|cadeia, padrão é "Administrador"|Dinâmica|Elimina um volume.| 
|EnumerateProperties |cadeia, padrão é "Admin\|\|User" | Dinâmica|Configuração de segurança para enumeração de propriedade de Nomeação. |
|EnumerarSubnomes |cadeia, padrão é "Admin\|\|User" |Dinâmica| Configuração de segurança para nomear a enumeração URI. |
|FileContent |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para transferência de ficheiros de cliente de loja de imagem (externa para cluster). |
|FileDownload |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para iniciação de descarregamento de ficheiros de cliente de loja de imagem (externa ao cluster). |
|FinishInfrastructureTask |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para tarefas de infraestrutura de acabamento. |
|GetChaosReport | cadeia, padrão é "Admin\|\|User" |Dinâmica| Busca o estado do Caos dentro de um determinado intervalo de tempo. |
|GetClusterConfiguration | cadeia, padrão é "Admin\|\|User" | Dinâmica|Induz getClusterConfiguration numa divisória. |
|GetClusterConfigurationUpgradeStatus | cadeia, padrão é "Admin\|\|User" |Dinâmica| Induz GetClusterConfigurationUpgradeStatus numa divisória. |
|GetFabricUpgradeStatus |cadeia, padrão é "Admin\|\|User" |Dinâmica| Configuração de segurança para o estado de atualização do cluster de sondagens. |
|GetFolderSize |cadeia, padrão é "Administrador" |Dinâmica|Configuração de segurança para o tamanho da pasta fileStoreService |
|GetNodeDeactivationStatus |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para verificar o estado de desativação. |
|GetNodeTransitionProgress | cadeia, padrão é "Admin\|\|User" |Dinâmica| Configuração de segurança para obter progressos num comando de transição de nó. |
|GetPartitionDataLossProgress | cadeia, padrão é "Admin\|\|User" | Dinâmica|Busca o progresso para uma chamada de perda de dados invocada. |
|GetPartitionQuorumLossProgress | cadeia, padrão é "Admin\|\|User" |Dinâmica| Busca o progresso para uma invocação de perda de quórum api chamada. |
|GetPartitionRestartProgress | cadeia, padrão é "Admin\|\|User" |Dinâmica| Busca o progresso para uma chamada de api de partição reata. |
|GetSecrets|cadeia, padrão é "Administrador"|Dinâmica|Obter valores secretos |
|GetServiceDescription |cadeia, padrão é "Admin\|\|User" |Dinâmica| Configuração de segurança para notificações de serviço de sondagem longa e descrições de serviços de leitura. |
|GetStagingLocation |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para a recuperação da localização do cliente da loja de imagens. |
|GetStoreLocation |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para a recuperação da localização da loja de clientes da loja de imagens. |
|GetUpgradeOrchestrationServiceState|cadeia, padrão é "Administrador"| Dinâmica|Induz GetUpgradeOrchestrationServiceState numa partição |
|GetUpgradesPendingApproval |cadeia, padrão é "Administrador" |Dinâmica| Induz GetUpgradesSpendApproval numa partição. |
|GetUpgradeStatus |cadeia, padrão é "Admin\|\|User" |Dinâmica| Configuração de segurança para o estado de atualização da aplicação de sondagens. |
|Lista Interna |cadeia, padrão é "Administrador" | Dinâmica|Configuração de segurança para a operação da lista de ficheiros do cliente da loja de imagens (interna). |
|InvokeContainerApi|string,padrão é "Administrador"|Dinâmica|Invocar recipiente API |
|InvokeInfrastructureCommand |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para comandos de gestão de tarefas de infraestrutura. |
|InvokeInfrastructureQuery |cadeia, padrão é "Admin\|\|User" | Dinâmica|Configuração de segurança para consulta de tarefas de infraestrutura. |
|Lista |cadeia, padrão é "Admin\|\|User" | Dinâmica|Configuração de segurança para a operação da lista de ficheiros do cliente da loja de imagens. |
|MoveNextFabricUpgradeDomain |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para retomar atualizações de cluster com um domínio de upgrade explícito. |
|MoveNextUpgradeDomain |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para retomar atualizações de aplicações com um domínio de upgrade explícito. |
|MoveReplicaControl |cadeia, padrão é "Administrador" | Dinâmica|Mova a réplica. |
|NameExists |cadeia, padrão é "Admin\|\|User" | Dinâmica|Configuração de segurança para nomear verificações de existência URI. |
|NodeControl |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para iniciar; parar; e reiniciando os nódosos. |
|NodeStateRemoved |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para reportar estado do nó removido. |
|Ping |cadeia, padrão é "Admin\|\|User" |Dinâmica| Configuração de segurança para pings de cliente. |
|PredeployPackageToNode |cadeia, padrão é "Administrador" |Dinâmica| Api pré-implantação. |
|PrefixResolveService |cadeia, padrão é "Admin\|\|User" |Dinâmica| Configuração de segurança para resolução de prefixo de serviço baseado em reclamações. |
|PropertyReadBatch |cadeia, padrão é "Admin\|\|User" |Dinâmica| Configuração de segurança para nomear operações de leitura de propriedade. |
|PropertyWriteBatch |cadeia, padrão é "Administrador" |Dinâmica|Configurações de segurança para nomear operações de escrita de propriedade. |
|ProvisionApplicationType |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para o fornecimento de tipo de aplicação. |
|ProvisionFabric |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para mSI e/ou cluster manifesto provisionamento. |
|Consulta |cadeia, padrão é "Admin\|\|User" |Dinâmica| Configuração de segurança para consultas. |
|RecoverPartition |cadeia, padrão é "Administrador" | Dinâmica|Configuração de segurança para recuperar uma partição. |
|Recuperação De Divisórias |cadeia, padrão é "Administrador" | Dinâmica|Configuração de segurança para recuperar divisórias. |
|RecoverServicePartitions |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para recuperar divisórias de serviço. |
|RecoverSystemPartitions |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para recuperar divisórias de serviço do sistema. |
|RemoveNodeDeactivations |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para reverter a desativação em vários nódosos. |
|ReportFabricUpgradeHealth |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para retomar as atualizações do cluster com o progresso atual da atualização. |
|Falha de relatório |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para reportar falha. |
|ReportHealth |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para reportar saúde. |
|ReportUpgradeHealth |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para retomar as atualizações da aplicação com o progresso atual da atualização. |
|ResetPartitionLoad |cadeia, padrão é "Admin\|\|User" |Dinâmica| Configuração de segurança para redefinir a carga para uma FailoverUnit. |
|ResolveNameOwner |cadeia, padrão é "Admin\|\|User" | Dinâmica|Configuração de segurança para resolver nomear o proprietário do URI. |
|ResolvePartition |cadeia, padrão é "Admin\|\|User" | Dinâmica|Configuração de segurança para a resolução de serviços do sistema. |
|ResolveService |cadeia, padrão é "Admin\|\|User" |Dinâmica| Configuração de segurança para resolução de serviços baseado em reclamações. |
|ResolveSystemService|cadeia, padrão é "Admin\|\|User"|Dinâmica| Configuração de segurança para a resolução de serviços do sistema |
|RollbackApplicationUpgrade |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para reverter atualizações de aplicações. |
|RollbackFabricUpgrade |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para reverter atualizações de cluster. |
|Notificações de Serviço |cadeia, padrão é "Admin\|\|User" |Dinâmica| Configuração de segurança para notificações de serviço baseadas em eventos. |
|SetUpgradeOrchestrationServiceState|cadeia, padrão é "Administrador"| Dinâmica|Induz SetUpgradeOrchestrationServiceState numa partição |
|StartApprovedUpgrades |cadeia, padrão é "Administrador" |Dinâmica| Induz startApprovedUpgrades numa divisória. |
|StartChaos |cadeia, padrão é "Administrador" |Dinâmica| Começa o Caos - se ainda não está iniciado. |
|StartClusterConfigurationUpgrade |cadeia, padrão é "Administrador" |Dinâmica| Induz startClusterConfigurationUpgrade numa divisória. |
|StartInfrastructureTask |cadeia, padrão é "Administrador" | Dinâmica|Configuração de segurança para iniciar tarefas de infraestrutura. |
|StartNodeTransition |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para iniciar uma transição de nó. |
|StartPartitionDataLoss |cadeia, padrão é "Administrador" |Dinâmica| Induz perda de dados numa partição. |
|StartPartitionQuorumLoss |cadeia, padrão é "Administrador" |Dinâmica| Induz a perda de quórum numa divisória. |
|StartPartitionRestart |cadeia, padrão é "Administrador" |Dinâmica| Simultaneamente reinicia algumas ou todas as réplicas de uma partição. |
|StopChaos |cadeia, padrão é "Administrador" |Dinâmica| Stops Chaos - se tiver sido iniciado. |
|ToggleVerboseServicePlacementHealthReporting | cadeia, padrão é "Admin\|\|User" |Dinâmica| Configuração de segurança para Toggling Verbose ServicePlacement HealthReporting. |
|UnprovisionApplicationType |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para o tipo de aplicação que não está a fornecer. |
|UnprovisionFabric |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para MSI e/ou Cluster Manifest não provisionando. |
|UnreliableTransportControl |cadeia, padrão é "Administrador" |Dinâmica| Transporte pouco fiável para adicionar e remover comportamentos. |
|UpdateService |cadeia, padrão é "Administrador" |Dinâmica|Configuração de segurança para atualizações de serviço. |
|Aplicação de upgrade |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para iniciar ou interromper atualizações de aplicações. |
|UpgradeComposeDeployment|cadeia, padrão é "Administrador"| Dinâmica|Melhora a implantação de composição |
|UpgradeFabric |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para iniciar upgrades de cluster. |
|Carregar |cadeia, padrão é "Administrador" | Dinâmica|Configuração de segurança para a operação de upload do cliente da loja de imagens. |

## <a name="securityclientcertificateissuerstores"></a>Security/ClientCertificateIssuerStores

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|PropertyGroup|EmitenteStoreKeyValueMap, padrão é Nenhum |Dinâmica|Lojas de certificados de emitente X509 para certificados de cliente; Nome = clienteIssuerCN; Valor = lista separada de lojas |

## <a name="securityclientx509names"></a>Security/ClientX509Names

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, padrão é Nenhum|Dinâmica|Esta é uma lista de pares "Nome" e "Valor". Cada "Nome" é de nome comum ou DnsName de certificados X509 autorizados para operações de cliente. Para um dado "Nome", "Valor" é uma lista separada de impressões digitais de certificado para emitente, se não vazia, o emitente direto dos certificados de cliente deve estar na lista.|

## <a name="securityclustercertificateissuerstores"></a>Lojas De Segurança/ClusterCertificateIssuerStores

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|PropertyGroup|EmitenteStoreKeyValueMap, padrão é Nenhum |Dinâmica|Lojas de certificados de emitente X509 para certificados de cluster; Nome = clusterIssuerCN; Valor = lista separada de lojas |

## <a name="securityclusterx509names"></a>Nomes de Segurança/ClusterX509

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, padrão é Nenhum|Dinâmica|Esta é uma lista de pares "Nome" e "Valor". Cada "Nome" é de nome comum ou DnsName de certificados X509 autorizados para operações de cluster. Para um dado "Nome", "Valor" é uma lista separada de impressões digitais de certificado para emitente, se não vazia, o emitente direto dos certificados de cluster deve estar na lista.|

## <a name="securityservercertificateissuerstores"></a>Lojas Security/ServerCertificateIssuerStores

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|PropertyGroup|EmitenteStoreKeyValueMap, padrão é Nenhum |Dinâmica|Lojas de certificados de emitente X509 para certificados de servidor; Nome = servidorIssuerCN; Valor = lista separada de lojas |

## <a name="securityserverx509names"></a>Nomes de segurança/servidorX509

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, padrão é Nenhum|Dinâmica|Esta é uma lista de pares "Nome" e "Valor". Cada "Nome" é de nome comum ou dnsName de certificados X509 autorizados para operações de servidor. Para um dado "Nome", "Value" é uma lista separada de impressões digitais de certificado para emitente, se não estiver vazia, o emitente direto dos certificados de servidor deve estar na lista.|

## <a name="setup"></a>Configurar

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|ContainerNetworkName|cadeia, padrão é ""| Estático |O nome da rede a utilizar ao criar uma rede de contentores.|
|ContainerNetworkSetup|bool, padrão é FALSO (Linux) e padrão é TRUE (Windows)| Estático |Se criar uma rede de contentores.|
|FabricDataRoot |String | Não é permitido |Diretório de raiz de dados de tecido de serviço. Padrão para Azure é d:\svcfab |
|FabricLogRoot |String | Não é permitido |Diretório de raiz de tronco de tecido de serviço. É aqui que são colocados troncos e vestígios sf. |
|NodesToBeRemoved|cadeia, padrão é ""| Dinâmica |Os nódosos que devem ser removidos como parte da atualização da configuração. (Apenas para implantações autónomas)|
|ServiceRunAsAccountName |String | Não é permitido |O nome da conta em que executar o serviço de hospedar tecido. |
|SkipContainerNetworkResetOnReboot|Bool, o padrão é FALSO|Não Permitido|Se não reiniciar a rede de contentores no reboot.|
|SkipFirewallConfiguration |Bool, o padrão é falso. | Não é permitido |Especifica se as definições de firewall precisam ou não de ser definidas pelo sistema. Isto só se aplica se estiver a utilizar firewall windows. Se estiver a utilizar firewalls de terceiros, então deve abrir as portas para o sistema e aplicações para usar |

## <a name="tokenvalidationservice"></a>TokenValidationService

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|Fornecedores |cadeia, padrão é "DSTS" |Estático|Comma lista separada de fornecedores de validação simbólica para ativar (fornecedores válidos são: DSTS; AAD). Atualmente, apenas um único fornecedor pode ser ativado a qualquer momento. |

## <a name="traceetw"></a>Vestígio/Etw

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|Nível |Int, padrão é 4 | Dinâmica |O nível de etw pode ter valores 1, 2, 3, 4. Para ser apoiado, deve manter o nível de rastreio em 4 |

## <a name="transactionalreplicator"></a>Replicador de transações

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|BatchAcknowledgementInterval | Tempo em segundos, padrão é 0.015 | Estático | Especifique a hora de tempo em segundos. Determina a quantidade de tempo que o replicador espera depois de receber uma operação antes de enviar de volta um reconhecimento. Outras operações recebidas durante este período terão os seus agradecimentos enviados numa única mensagem-> reduzindo o tráfego de rede, mas potencialmente reduzindo a produção do replicador. |
|MaxCopyQueueSize |Uint, padrão é 16384 | Estático |Este é o valor máximo que define o tamanho inicial para a fila que mantém as operações de replicação. Note que deve ser uma potência de 2. Se durante o tempo de funcionamento a fila crescer para este tamanho, a operação será acelerada entre os replicadores primários e secundários. |
|MaxPrimaryReplicationQueueMemorySize |uint, padrão é 0 | Estático |Este é o valor máximo da fila de replicação primária em bytes. |
|MaxPrimaryReplicationQueueSize |Uint, padrão é 8192 | Estático |Este é o número máximo de operações que podem existir na fila de replicação primária. Note que deve ser uma potência de 2. |
|MaxReplicationMessageSize |Uint, padrão é 52428800 | Estático | Tamanho máximo da mensagem das operações de replicação. O padrão é de 50MB. |
|MaxSecondaryReplicationQueueMemorySize |uint, padrão é 0 | Estático |Este é o valor máximo da fila de replicação secundária em bytes. |
|MaxSecondaryReplicationQueueSize |Uint, padrão é 16384 | Estático |Este é o número máximo de operações que podem existir na fila de replicação secundária. Note que deve ser uma potência de 2. |
|ReplicatorAddress |cadeia, padrão é "localhost:0" | Estático | O ponto final sob a forma de uma corda -'IP:Porta' que é utilizada pelo Replicador de Tecidos do Windows para estabelecer ligações com outras réplicas para enviar/receber operações. |

## <a name="transport"></a>Transporte
| **Parâmetro** | **Valores Permitidos** |**Política de upgrade** |**Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|ConnectionOpenTimeout|TimeSpan, o padrão é comum:TimeSpan:FromSeconds (60)|Estático|Especifique a hora de tempo em segundos. Tempo para configuração de ligação tanto do lado da entrada como da aceitação (incluindo a negociação de segurança em modo seguro) |
|FrameHeaderErrorCheckingEnabled|Bool, o padrão é VERDADE|Estático|Definição predefinida para verificação de erros no cabeçalho do quadro no modo não seguro; a definição do componente sobrepõe-se a isto. |
|MessageErrorCheckingEnabled|bool,padrão é FALSO|Estático|Definição predefinida para verificar erros no cabeçalho da mensagem e no corpo no modo não seguro; a definição do componente sobrepõe-se a isto. |
|ResolveOption|cadeia, padrão é "não especificado"|Estático|Determina como a FQDN é resolvida.  Os valores válidos são "não especificados/ipv4/ipv6". |
|Tempo de envio|TimeSpan, o padrão é comum:TimeSpan:FromSeconds (300)|Dinâmica|Especifique a hora de tempo em segundos. Envie tempo para detetar a ligação presa. Os relatórios de falhas da TCP não são fiáveis em alguns ambientes. Isto pode ter de ser ajustado de acordo com a largura de banda de rede disponível e o tamanho dos dados de saída disponíveis (\*MaxMessageSize\/\*SendQueueSizeLimit). |

## <a name="upgradeorchestrationservice"></a>UpgradeOrchestrationService

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|Upgrade automáticoEnabled | Bool, o padrão é verdade |Estático| Sondagem automática e ação de atualização com base num ficheiro de estado-objetivo. |
|AutoupgradeInstallEnabled|Bool, o padrão é FALSO|Estático|Votação automática, fornecimento e instalação de medidas de atualização de código saem com base num ficheiro de estado-objetivo.|
|GoalStateExpirationReminderInDays|int, padrão é 30|Estático|Define o número de dias restantes após o qual deve ser mostrado o lembrete do estado-objectivo.|
|MinReplicaSetSize |int, padrão é 0 |Estático |O MinReplicaSetSize para UpgradeOrchestrationService.|
|PlacementConstraints | cadeia, padrão é "" |Estático| Os Limites de Colocação para UpgradeOrchestrationService. |
|QuorumLossWaitDuration | Tempo em segundos, padrão é MaxValue |Estático| Especifique a hora de tempo em segundos. O QuorumLossWaitDura para upgradeOrquestrationService. |
|ReplicaRestartWaitDuration | Tempo em segundos, padrão é de 60 minutos|Estático| Especifique a hora de tempo em segundos. A Duração da Reversão da Réplica para UpgradeOrchestrationService. |
|StandByReplicaKeepDuration | Tempo em segundos, padrão é de 60*24*7 minutos |Estático| Especifique a hora de tempo em segundos. O StandByReplicaKeepDura para upgradeOrchestrationService. |
|TargetReplicaSetSize |int, padrão é 0 |Estático |O TargetReplicaSetSize para UpgradeOrchestrationService. |
|ActualizaçãoReNecessária | Bool, o padrão é falso. | Estático|A definição para fazer a atualização do código requer a aprovação do administrador antes de prosseguir. |

## <a name="upgradeservice"></a>UpgradeService

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|BaseUrl | cadeia, padrão é "" |Estático|BaseUrl para UpgradeService. |
|ClusterId | cadeia, padrão é "" |Estático|ClusterId para UpgradeService. |
|CoordenadorType | cadeia, padrão é "WUTest"|Não é permitido|O CoordenadorType para UpgradeService. |
|MinReplicaSetSize | Int, padrão é 2 |Não é permitido| O MinReplicaSetSize para UpgradeService. |
|OnlyBaseUpgrade | Bool, o padrão é falso. |Dinâmica|OnlyBaseUpgrade para UpgradeService. |
|PlacementConstraints |cadeia, padrão é "" |Não é permitido|O serviço PlacementConstraints for Upgrade. |
|PollIntervalInSeconds|Timepan, predefinição é comum:TimeSpan::FromSeconds (60) |Dinâmica|Especifique a hora de tempo em segundos. O intervalo entre a sondagem upgradeService para operações de gestão ARM. |
|TargetReplicaSetSize | Int, padrão é 3 |Não é permitido| O TargetReplicaSetSize para UpgradeService. |
|TestCabFolder | cadeia, padrão é "" |Estático| TestCabFolder para UpgradeService. |
|X509FindType | cadeia, padrão é ""|Dinâmica| X509FindType para UpgradeService. |
|X509FindValue | cadeia, padrão é "" |Dinâmica| X509FindValue para UpgradeService. |
|X509SecondaryFindValue | cadeia, padrão é "" |Dinâmica| X509SecondaryFindValue para UpgradeService. |
|X509StoreLocation | cadeia, padrão é "" |Dinâmica| X509StoreLocation para UpgradeService. |
|X509StoreName | cadeia, padrão é "O meu"|Dinâmica|X509StoreName para UpgradeService. |

## <a name="userservicemetriccapacities"></a>UserServiceMetricCapacities
| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|PropertyGroup| UserServiceMetricCapacitiesMap, padrão é Nenhum | Estático | Uma coleção de limites de governança de recursos de serviços de utilizador Tem de ser estática, uma vez que afeta a lógica de Deteção Automática |

## <a name="next-steps"></a>Passos seguintes
Para mais informações, consulte [Aconfiguração de um cluster Azure](service-fabric-cluster-config-upgrade-azure.md) e [atualize a configuração de um cluster autónomo](service-fabric-cluster-config-upgrade-windows-server.md).
