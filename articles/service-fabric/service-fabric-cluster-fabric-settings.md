---
title: Alterar configurações de cluster de tecido de serviço Azure
description: Este artigo descreve as definições de tecido e as políticas de upgrade de tecido que pode personalizar.
ms.topic: reference
ms.date: 08/30/2019
ms.openlocfilehash: fbd6c9503e409473a87c58202eb88d77716441f9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89055125"
---
# <a name="customize-service-fabric-cluster-settings"></a>Personalizar as definições do cluster do Service Fabric
Este artigo descreve as várias definições de tecido para o seu cluster de Tecido de Serviço que pode personalizar. Para os clusters alojados no Azure, pode personalizar as definições através do [portal Azure](https://portal.azure.com) ou utilizando um modelo de Gestor de Recursos Azure. Para obter mais informações, consulte [atualizar a configuração de um cluster Azure](service-fabric-cluster-config-upgrade-azure.md). Para clusters autónomos, personaliza as definições atualizando o *ClusterConfig.jsno* ficheiro e executando uma atualização de configuração no seu cluster. Para obter mais informações, consulte [atualizar a configuração de um cluster autónomo](service-fabric-cluster-config-upgrade-windows-server.md).

Existem três políticas de atualização diferentes:

- **Dinâmica** – As alterações a uma configuração dinâmica não provocam qualquer reinício de processos de tecido de serviço ou de processos de anfitrião de serviço. 
- **Estática** – As alterações a uma configuração estática farão com que o nó do Tecido de Serviço reinicie para consumir a alteração. Os serviços nos nós serão reiniciados.
- **NotAllowed** – Estas definições não podem ser modificadas. A alteração destas definições requer que o cluster seja destruído e que seja criado um novo cluster. 

Segue-se uma lista de configurações de Tecido que pode personalizar, organizada por secção.

## <a name="applicationgatewayhttp"></a>ApplicationGateway/Http

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|AplicaçãoCertificateValidationPolicy|cadeia, padrão é "Nenhum"|Estático| Isto não valida o certificado do servidor; suceder ao pedido. Consulte as impressões config ServiceCertificateThumb para a lista separada por vírgula de impressões digitais dos certificados remotos em que o representante invertido pode confiar. Consulte o config ServiceCommonNameAndIssuer para o nome do assunto e impressão digital do emitente dos certificados remotos em que o representante inverso pode confiar. Para saber mais, consulte [a ligação de procuração inversa](service-fabric-reverseproxy-configure-secure-communication.md#secure-connection-establishment-between-the-reverse-proxy-and-services). |
|BodyChunksize |Uint, o padrão é 16384 |Dinâmica| Dá o tamanho do pedaço em bytes usados para ler o corpo. |
|CrlCheckingFlag|uint, o padrão é 0x40000000 |Dinâmica| Bandeiras para validação da cadeia de certificados de aplicação/serviço; por exemplo, verificação de CRL 0x100000000 CERT_CHAIN_REVOCATION_CHECK_END_CERT 0x200000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN 0x400000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT 0x800000000000000000000000000000000000000000000000000 CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY Configuração para 0 desativa CRL verificando a lista completa de valores suportados é documentada por dwFlags of CertGetCertificateChain: https://msdn.microsoft.com/library/windows/desktop/aa376078(v=vs.85).aspx  |
|DefaultHttpRequestTimeout |Tempo em segundos. padrão é 120 |Dinâmica|Especifique a timepan em segundos.  Dá o tempo limite de pedido padrão para os pedidos de http que estão sendo processados no gateway da aplicação http. |
|AvançadoClientCertificato|bool, o padrão é FALSO|Dinâmica|Quando definido como falso, o representante inverso não solicitará a certidão do cliente. Quando definido como verdadeiro, o representante inverso solicitará o certificado do cliente durante o aperto de mão TLS e encaminhará a cadeia de formato PEM codificada de base64 para o serviço num cabeçalho denominado X-Client-Certificate.O serviço pode falhar o pedido com o código de estado apropriado após a inspeção dos dados do certificado. Se isso for verdade e o cliente não apresentar um certificado, o representante inverso irá encaminhar um cabeçalho vazio e deixar o serviço tratar do caso. O representante inverso funcionará como uma camada transparente. Para saber mais, consulte [Configurar a autenticação do certificado do cliente.](service-fabric-reverseproxy-configure-secure-communication.md#setting-up-client-certificate-authentication-through-the-reverse-proxy) |
|GatewayAuthCredentialType |cadeia, padrão é "Nenhum" |Estático| Indica o tipo de credenciais de segurança a utilizar no ponto final do gateway de aplicações http Os valores válidos são Nenhum/X509. |
|GatewayX509CertificateFindType |cadeia, padrão é "FindByThumbprint" |Dinâmica| Indica como procurar certificado na loja especificado por GatewayX509CertificateStoreName Valor suportado: FindByThumbprint; FindBySubjectName. |
|GatewayX509CertificateFindValue | cadeia, padrão é "" |Dinâmica| Valor do filtro de pesquisa utilizado para localizar o certificado de gateway de aplicações http. Este certificado encontra-se configurado no ponto final do https e pode também ser utilizado para verificar a identidade da app, caso seja necessário pelos serviços. FindValue é procurado primeiro; e se isso não existir; FindValueSecondary é olhado para cima. |
|GatewayX509CertificateFindValueSecondary | cadeia, padrão é "" |Dinâmica|Valor do filtro de pesquisa utilizado para localizar o certificado de gateway de aplicações http. Este certificado encontra-se configurado no ponto final do https e pode também ser utilizado para verificar a identidade da app, caso seja necessário pelos serviços. FindValue é procurado primeiro; e se isso não existir; FindValueSecondary é olhado para cima.|
|Gatewayx509CertificateStoreName |cadeia, padrão é "My" |Dinâmica| Nome da loja de certificados X.509 que contém certificado para http app gateway. |
|httpRequestConnectTimeout|TimeSpan, o predefinição é Comum::TimeSpan::FromSeconds(5)|Dinâmica|Especifique a timepan em segundos.  Dá o tempo limite de ligação para os pedidos de http que estão a ser enviados a partir do portal da aplicação http.  |
|IgnoreCrlOfflineError|bool, o padrão é VERDADEIRO|Dinâmica|Se ignorar erro offline CRL para verificação de certificado de aplicação/serviço. |
|IsEnabled |Bool, o padrão é falso. |Estático| Ativa/Desativa o HttpApplicationGateway. HttpApplicationGateway é desativado por padrão e este config precisa de ser configurado para o permitir. |
|NúmeroOfParallelOperações | Uint, o padrão é 5000 |Estático|Número de leituras para publicar na fila do servidor http. Isto controla o número de pedidos simultâneos que podem ser satisfeitos pelo HttpGateway. |
|RemoveServserversResponseHeaders|cadeia, predefinição é "Data; Servidor"|Estático|Semi-cólon/lista separada de vírgula de cabeçalhos de resposta que serão removidos da resposta de serviço; antes de encaminhá-lo para o cliente. Se isto estiver definido para corda vazia; passar todos os cabeçalhos devolvidos pelo serviço como é. i.e não substitua a data e o servidor |
|ResolveServiceBackoffInterval |Tempo em segundos, o padrão é 5 |Dinâmica|Especifique a timepan em segundos.  Dá o intervalo de back-off predefinido antes de voltar a tentar uma operação de serviço de resolução falhada. |
|SecureOnlyMode|bool, o padrão é FALSO|Dinâmica| SecureOnlyMode: true: Reverse Proxy só irá encaminhar para serviços que publiquem pontos finais seguros. falso: Reverse Proxy pode encaminhar pedidos para pontos finais seguros/não seguros. Para saber mais, consulte [a lógica de seleção de pontos finais de reverse proxy](service-fabric-reverseproxy-configure-secure-communication.md#endpoint-selection-logic-when-services-expose-secure-as-well-as-unsecured-endpoints).  |
|ServiceCertificateThumbprints|cadeia, padrão é ""|Dinâmica|A lista separada por vírgulas de impressões digitais dos certificados remotos em que o representante invertido pode confiar. Para saber mais, consulte [a ligação de procuração inversa](service-fabric-reverseproxy-configure-secure-communication.md#secure-connection-establishment-between-the-reverse-proxy-and-services). |

## <a name="applicationgatewayhttpservicecommonnameandissuer"></a>ApplicationGateway/Http/ServiceCommonNameAndIssuer

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|Grupo de Propriedades|X509NameMap, padrão é Nenhum|Dinâmica| Nome do sujeito e impressão digital do emitente dos certificados remotos em que o representante inverso pode confiar. Para saber mais, consulte [a ligação de procuração inversa](service-fabric-reverseproxy-configure-secure-communication.md#secure-connection-establishment-between-the-reverse-proxy-and-services). |

## <a name="backuprestoreservice"></a>BackupRestoreService

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|MinReplicaSetsize|int, o padrão é 0|Estático|O MinReplicaSetSize para BackupRestoreService |
|ColocaçãoConstratas|cadeia, padrão é ""|Estático|    Os PlacementConstraints para o serviço BackupRestore |
|SecretEncryptionCertThumbprint|cadeia, padrão é ""|Dinâmica|Impressão digital do certificado X509 de encriptação secreta |
|SegredoEncryptionCertX509StoreName|cadeia, valor recomendado é "My" (sem predefinição) |    Dinâmica|    Isto indica o certificado a utilizar para encriptação e desencriptação de credenciais Nome da loja de certificados X.509 que é usada para encriptar credenciais de loja de desencriptação usadas pelo serviço Backup Restore |
|TargetReplicaSetsize|int, o padrão é 0|Estático| O TargetReplicaSetSize para BackupRestoreService |

## <a name="clustermanager"></a>ClusterManager

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|Permitir As Políticas DoResto DeResta | Bool, o padrão é falso. |Dinâmica|Se são ou não permitidas políticas de classificação de upgrade personalizadas. Isto é utilizado para realizar uma atualização de 2 fases permitindo esta função. O Tecido de Serviço 6.5 adiciona suporte para especificar a política de classificação para os domínios de atualização durante as atualizações de clusters ou aplicações. As políticas apoiadas são numéricas, lexicográficas, inversanuméricas e reverseLexicográficas. O padrão é numérico. Para poder utilizar esta função, o conjunto manifesto clusterManager/ AllowCustomUpgradeSortPolicies deve ser definido como um segundo passo de atualização config após o código SF 6.5 ter concluído a atualização. É importante que isto seja feito em duas fases, caso contrário a atualização de código pode ficar confusa sobre a ordem de atualização durante a primeira atualização.|
|EnableDefaultServicesUpgrade | Bool, o padrão é falso. |Dinâmica|Ativar a atualização dos serviços predefinidos durante a atualização da aplicação. As descrições de serviço predefinidos seriam substituídas após a atualização. |
|FabricUpgradeHealthCheckInterval |Tempo em segundos, o padrão é 60 |Dinâmica|A frequência da verificação do estado de saúde durante uma atualização monitorizada do tecido |
|FabricUpgradeStatusPollInterval |Tempo em segundos, o padrão é 60 |Dinâmica|A frequência das sondagens para o estado de atualização do Tecido. Este valor determina a taxa de atualização para qualquer chamada GetFabricUpgradeProgress |
|ImageBuilderTimeoutBuffer |Tempo em segundos, o padrão é 3 |Dinâmica|Especifique a timepan em segundos. O tempo que permite que erros de tempo específicos do Construtor de Imagem regressem ao cliente. Se este tampão for muito pequeno; em seguida, o cliente cronometra antes do servidor e obtém um erro genérico de tempo limite. |
|InfrastructureTaskHealthCheckRetryTimeout | Tempo em segundos, o padrão é 60 |Dinâmica|Especifique a timepan em segundos. O tempo para passar a tentar novamente os controlos de saúde falhados durante o pós-processamento de uma tarefa de infraestrutura. Observar um exame de saúde aprovado irá reiniciar este temporizador. |
|InfrastructureTaskHealthCheckStableDuration | Tempo em segundos, o padrão é 0|Dinâmica| Especifique a timepan em segundos. O tempo de observação consecutivo dos controlos de saúde passados antes do pós-processamento de uma tarefa de infraestrutura termina com sucesso. Observar um exame de saúde falhado irá reiniciar este temporizador. |
|InfrastructureTaskHealthCheckWaitDuration |Tempo em segundos, o padrão é 0|Dinâmica| Especifique a timepan em segundos. O tempo de espera para iniciar os controlos de saúde após o pós-processamento de uma tarefa de infraestrutura. |
|InfrastructureTaskProcessingInterval | Tempo em segundos, o padrão é 10 |Dinâmica|Especifique a timepan em segundos. O intervalo de processamento utilizado pela máquina de processamento de tarefas de infraestrutura. |
|MaxCommunicationTimeout |Tempo em segundos, o padrão é 600 |Dinâmica|Especifique a timepan em segundos. O prazo máximo para as comunicações internas entre o ClusterManager e outros serviços de sistema (isto é, os serviços de sistemas). Serviço de Nomeação; Gestor de Failover e etc.). Este tempo limite deve ser menor do que o MaxOperationTimeout global (uma vez que pode haver múltiplas comunicações entre componentes do sistema para cada operação do cliente). |
|MaxDataMigrationTimeout |Tempo em segundos, o padrão é 600 |Dinâmica|Especifique a timepan em segundos. O prazo máximo para as operações de recuperação da migração de dados após a realização de uma atualização do Tecido. |
|MaxOperationRetryDelay |Tempo em segundos, o padrão é 5|Dinâmica| Especifique a timepan em segundos. O atraso máximo para as retrações internas quando as falhas são encontradas. |
|MaxOperationTimeout |Tempo em segundos, o padrão é MaxValue |Dinâmica| Especifique a timepan em segundos. O tempo limite máximo global para operações de processamento interno no ClusterManager. |
|MaxTimeoutRetryBuffer | Tempo em segundos, o padrão é 600 |Dinâmica|Especifique a timepan em segundos. O tempo máximo de funcionamento quando se reagem internamente devido a intervalos de tempo é `<Original Time out> + <MaxTimeoutRetryBuffer>` . O tempo limite adicional é adicionado em incrementos do MinOperationTimeout. |
|MinOperationTimeout | Tempo em segundos, o padrão é 60 |Dinâmica|Especifique a timepan em segundos. O tempo mínimo global para operações de processamento interno no ClusterManager. |
|MinReplicaSetsize |Int, o padrão é 3 |Não é permitido|O MinReplicaSetSize para ClusterManager. |
|ColocaçãoConstratas | cadeia, padrão é "" |Não é permitido|Os Conjuntos de Colocação para ClusterManager. |
|QurumLossWaitDuration |Tempo em segundos, o padrão é MaxValue |Não é permitido| Especifique a timepan em segundos. O QuorumLossWaitDuration para ClusterManager. |
|ReplicaRestartWaitDuration |Tempo em segundos, o padrão é (60.0 \* 30)|Não é permitido|Especifique a timepan em segundos. A ReplicaRestartWaitDuration para ClusterManager. |
|ReplicaSetCheckTimeoutRollbackOverride |Tempo em segundos, o padrão é 1200 |Dinâmica| Especifique a timepan em segundos. Se replicarSetCheckTimeout é definido para o valor máximo de DWORD; então é ultrapassado com o valor deste config para fins de reversão. O valor utilizado para o roll-forward nunca é ultrapassado. |
|SkipRollbackUpdateDefaultService | Bool, o padrão é falso. |Dinâmica|O CM saltará para reverter os serviços de predefinição atualizados durante a reversão da atualização da aplicação. |
|StandByReplicaKeepDuration | Tempo em segundos, o padrão é (3600.0 \* 2)|Não é permitido|Especifique a timepan em segundos. A StandByReplicaKeepDuration para ClusterManager. |
|TargetReplicaSetsize |Int, o padrão é 7 |Não é permitido|O TargetReplicaSetSize para ClusterManager. |
|UpgradeHealthCheckInterval |Tempo em segundos, o padrão é 60 |Dinâmica|A frequência dos controlos do estado de saúde durante uma atualização de aplicações monitorizadas |
|UpgradeStatusPollInterval |Tempo em segundos, o padrão é 60 |Dinâmica|A frequência das sondagens para o estado de atualização de candidaturas. Este valor determina a taxa de atualização para qualquer chamada GetApplicationUpgradeProgress |
|Recíproco de Conclusão | Bool, o padrão é falso. |Dinâmica| Pedido completo do cliente quando aceite pela CM. |

## <a name="common"></a>Common

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|PerfMonitorInterval |Tempo em segundos, o padrão é 1 |Dinâmica|Especifique a timepan em segundos. Intervalo de monitorização do desempenho. A definição para 0 ou valor negativo desativa a monitorização. |

## <a name="defragmentationemptynodedistributionpolicy"></a>DefragmentationEmptyNodeDistributionPolicy
| **Parâmetro** | **Valores Permitidos** |**Política de Upgrade**| **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|Grupo de Propriedades|KeyIntegerValueMap, padrão é Nenhum|Dinâmica|Especifica a desfragmentação da política segue-se ao esvaziar os nós. Para uma determinada métrica 0 indica que a SF deve tentar desfragmentar os nós uniformemente em UDs e FDs; 1 indica apenas que os nóns devem ser desfragmentados |

## <a name="defragmentationmetrics"></a>DefragmentaçãoMétrica
| **Parâmetro** | **Valores Permitidos** |**Política de Upgrade**| **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|Grupo de Propriedades|KeyBoolValueMap, padrão é Nenhum|Dinâmica|Determina o conjunto de métricas que devem ser usadas para desfragmentação e não para o equilíbrio de carga. |

## <a name="defragmentationmetricspercentornumberofemptynodestriggeringthreshold"></a>DefragmentationMetricsPercentOrNumberOfEmptyNodesTriggeringThreshold
| **Parâmetro** | **Valores Permitidos** |**Política de Upgrade**| **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|Grupo de Propriedades|KeyDoubleValueMap, padrão é nenhum|Dinâmica|Determina o número de nós livres necessários para considerar o cluster defragmentado especificando quer por cento no intervalo [0.0 - 1.0] ou o número de nós vazios como número >= 1,0 |

## <a name="diagnostics"></a>Diagnóstico

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|AdminOnlyHttpAudit |Bool, o padrão é verdadeiro | Dinâmica | Excluir pedidos HTTP que não impactem o estado do cluster da auditoria. Atualmente; Apenas estão excluídos os pedidos do tipo "GET"; mas isto está sujeito a mudanças. |
|AppDiagnosticStoreAccessRequiresImpersonation |Bool, o padrão é verdadeiro | Dinâmica |É ou não necessária a personificação ao aceder às lojas de diagnóstico em nome da aplicação. |
|AppEtwTraceDeletionAgeInDays |Int, o padrão é 3 | Dinâmica |Número de dias após o qual eliminamos ficheiros ETL antigos que contêm vestígios de ETW da aplicação. |
|ApplicationLogsFormatVersion |Int, o padrão é 0 | Dinâmica |Versão para formato de registos de aplicações. Os valores suportados são 0 e 1. A versão 1 inclui mais campos do registo do evento ETW do que a versão 0. |
|AuditHttpRequests |Bool, o padrão é falso. | Dinâmica | Ligue a auditoria HTTP ou desligada. O objetivo da auditoria é ver as atividades que foram realizadas contra o cluster; incluindo quem iniciou o pedido. Note que esta é a melhor tentativa de registo; e pode ocorrer perda de vestígios. Os pedidos HTTP com autenticação "Utilizador" não são registados. |
|CaptureHttpTelemetry|Bool, o padrão é verdadeiro | Dinâmica | Ligue ou desligue a telemetria HTTP. O objetivo da telemetria é que o Service Fabric seja capaz de capturar dados de telemetria para ajudar a planear o trabalho futuro e identificar áreas problemáticas. A telemetria não regista quaisquer dados pessoais ou o organismo de pedido. A telemetria captura todos os pedidos HTTP, salvo configuração em contrário. |
|ClusterId |Cadeia | Dinâmica |A identificação única do aglomerado. Isto é gerado quando o cluster é criado. |
|Instâncias do Consumidor |Cadeia | Dinâmica |A lista de casos de consumidores da DCA. |
|DiskFullSafetySpaceInMB |Int, o padrão é 1024 | Dinâmica |Espaço restante em disco em MB para proteger da utilização por DCA. |
|EnableCircularTraceSsion |Bool, o padrão é falso. | Estático |A bandeira indica se devem ser utilizadas sessões circulares de rastreio. |
|EnablePlatformEventsFileSink |Bool, o padrão é falso. | Estático |Ativar/Desativar eventos de plataforma que estão a ser escritos em disco |
|EnableTelemetry |Bool, o padrão é verdadeiro | Dinâmica |Isto vai ativar ou desativar a telemetria. |
|FalhasOnlyHttpTelemetry | Bool, o padrão é falso. | Dinâmica | Se a captura de telemetria HTTP estiver ativada; capturar apenas pedidos falhados. Isto é para ajudar a reduzir o número de eventos gerados para a telemetria. |
|HttpTelemetryCapturePercentage | int, o padrão é 50 | Dinâmica | Se a captura de telemetria HTTP estiver ativada; capturar apenas uma percentagem aleatória de pedidos. Isto é para ajudar a reduzir o número de eventos gerados para a telemetria. |
|MaxDiskQuotaInMB |Int, o padrão é 65536 | Dinâmica |Quota de disco em MB para ficheiros de registo de tecido do Windows. |
|ProdutoresInstances |Cadeia | Dinâmica |A lista de casos de produtores da DCA. |

## <a name="dnsservice"></a>DnsService
| **Parâmetro** | **Valores Permitidos** |**Política de Upgrade**| **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|EnablePartitionedQuery|bool, o padrão é FALSO|Estático|A bandeira permite o suporte a consultas de DNS para serviços divididos. A funcionalidade é desligada por defeito. Para mais informações, consulte [o Serviço DNS do Tecido de Serviço.](service-fabric-dnsservice.md)|
|InstânciaCount|int, o padrão é -1|Estático|O valor predefinido é de -1, o que significa que o DnsService está a funcionar em todos os nós. A OneBox precisa que isto seja definido para 1 uma vez que o DnsService utiliza a porta 53 bem conhecida, pelo que não pode ter várias instâncias na mesma máquina.|
|IsEnabled|bool, o padrão é FALSO|Estático|Ativa/Desativa o DnsService. O DnsService é desativado por padrão e este config precisa de ser configurado para o permitir. |
|Acordo de Partição|cadeia, padrão é "--"|Estático|Controla o valor da cadeia de prefixo de partição em consultas DNS para serviços divididos. O valor: <ul><li>Deve ser compatível com o RFC, uma vez que fará parte de uma consulta de DNS.</li><li>Não deve conter um ponto, '.', uma vez que o ponto interfere com o comportamento do sufixo DNS.</li><li>Não deve ter mais de 5 caracteres.</li><li>Não pode ser uma corda vazia.</li><li>Se a definição partiçãoPrefixo for ultrapassada, então o sufixo PartitionS deve ser ultrapassado e vice-versa.</li></ul>Para mais informações, consulte [o Serviço DNS de Tecido de Serviço.](service-fabric-dnsservice.md)|
|Sufixo de divisórias|cadeia, padrão é ""|Estático|Controla o valor da cadeia de sufixo de partição em consultas DNS para serviços divididos. O valor: <ul><li>Deve ser compatível com o RFC, uma vez que fará parte de uma consulta de DNS.</li><li>Não deve conter um ponto, '.', uma vez que o ponto interfere com o comportamento do sufixo DNS.</li><li>Não deve ter mais de 5 caracteres.</li><li>Se a definição partiçãoPrefixo for ultrapassada, então o sufixo PartitionS deve ser ultrapassado e vice-versa.</li></ul>Para mais informações, consulte [o Serviço DNS de Tecido de Serviço.](service-fabric-dnsservice.md) |

## <a name="eventstoreservice"></a>EventStoreService

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|MinReplicaSetsize|int, o padrão é 0|Estático|O MinReplicaSetSize para o serviço EventStore |
|ColocaçãoConstratas|cadeia, padrão é ""|Estático|    Os PlacementConstraints para o serviço EventStore |
|TargetReplicaSetsize|int, o padrão é 0|Estático| O targetReplicaSetSize para o serviço EventStore |

## <a name="fabricclient"></a>TecidoCliente

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|ConnectionInitializationTimeout |Tempo em segundos, o padrão é 2 |Dinâmica|Especifique a timepan em segundos. Intervalo de tempo de ligação para cada vez que o cliente tenta abrir uma ligação ao gateway.|
|HealthOperationTimeout |Tempo em segundos, o padrão é 120 |Dinâmica|Especifique a timepan em segundos. O tempo limite para uma mensagem de relatório enviada ao Health Manager. |
|HealthReportRetrySendInterval |Tempo em segundos, padrão é 30, mínimo é 1 |Dinâmica|Especifique a timepan em segundos. O intervalo em que a componente de reporte reencoende relatórios de saúde acumulados ao Health Manager. |
|HealthReportSendInterval |Tempo em segundos, o padrão é 30 |Dinâmica|Especifique a timepan em segundos. O intervalo em que a componente de reporte envia relatórios de saúde acumulados ao Health Manager. |
|KeepAliveIntervalInSeconds |Int, o padrão é 20 |Estático|O intervalo em que o transporte FabricClient envia mensagens vivas para o portal. Para 0; keepAlive é desativado. Deve ser um valor positivo. |
|MaxFileSenderThreads |Uint, o padrão é 10 |Estático|O número máximo de ficheiros que são transferidos em paralelo. |
|NodeAddresses |cadeia, padrão é "" |Estático|Uma coleção de endereços (cadeias de ligação) em diferentes nós que podem ser usados para comunicar com o Serviço de Nomeação. Inicialmente, o Cliente liga-se selecionando aleatoriamente um dos endereços. Se for fornecida mais do que uma cadeia de ligação e uma ligação falhar devido a um erro de comunicação ou de tempo limite; o Cliente muda para utilizar o próximo endereço sequencialmente. Consulte a secção de relemistura do Endereço de Nomeação para obter mais informações sobre a semântica de retrações. |
|DivistionLocationCacheLimit |Int, o padrão é 100000 |Estático|Número de divisórias em cache para resolução de serviço (definido para 0 sem limite). |
|RetripBackoffInterval |Tempo em segundos, o padrão é 3 |Dinâmica|Especifique a timepan em segundos. O intervalo de recuo antes de voltar a tentar a operação. |
|ServiceChangePollInterval |Tempo em segundos, o padrão é 120 |Dinâmica|Especifique a timepan em segundos. O intervalo entre as sondagens consecutivas para alterações de serviço do cliente para a porta de entrada para chamadas de chamadas de alterações de serviço registadas. |

## <a name="fabrichost"></a>FabricHost

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|ActivationMaxFailureCount |Int, o padrão é 10 |Dinâmica|Esta é a contagem máxima para a qual o sistema vai voltar a tentar a ativação falhada antes de desistir. |
|ActivationMaxRetryInterval |Tempo em segundos, o padrão é 300 |Dinâmica|Especifique a timepan em segundos. Intervalo de retíria máximo para ativação. Em cada falha contínua, o intervalo de repetição é calculado como Min( ActivationMaxRetryInterval; Contagem de falhas contínuas * ActivationRetryBackoffInterval). |
|ActivationRetryBackoffInterval |Tempo em segundos, o padrão é 5 |Dinâmica|Especifique a timepan em segundos. Intervalo de backoff em cada falha de ativação;Em cada falha de ativação contínua o sistema irá re-tentar a ativação até ao MaxActivationFailureCount. O intervalo de repetição em cada tentativa é um produto de falha de ativação contínua e do intervalo de recuo da ativação. |
|EnableRestartManagement |Bool, o padrão é falso. |Dinâmica|Isto é para permitir o reinício do servidor. |
|EnableServiceFabricAutomaticUpdates |Bool, o padrão é falso. |Dinâmica|Isto é para permitir a atualização automática do tecido através do Windows Update. |
|EnableServiceFabricBaseUpgrade |Bool, o padrão é falso. |Dinâmica|Isto é para ativar a atualização base para o servidor. |
|FalhaReportingExpeditedReportingIntervalEnabled | Bool, o padrão é verdadeiro | Estático | Permite taxas de carregamento mais rápidas em DCA quando o FabricHost está no modo relatório de falhas. |
|FalhaReportingTimeout | TimeSpan, o padrão é Comum::TimeSpan::FromSeconds(60) | Estático |Especifique a timepan em segundos. Timeout para relatórios de falha da DCA no caso FabricHost encontra uma falha de arranque em fase inicial. | 
|RunDCAOnStartupFailure | Bool, o padrão é verdadeiro | Estático |Determina se deve lançar O DCA para carregar registos quando enfrenta problemas de arranque no FabricHost. | 
|StartTimeout |Tempo em segundos, o padrão é 300 |Dinâmica|Especifique a timepan em segundos. Tempo para arranque de empresas de tecidos. |
|StopTimeout |Tempo em segundos, o padrão é 300 |Dinâmica|Especifique a timepan em segundos. O tempo limite para a ativação do serviço hospedado; desativação e atualização. |

## <a name="fabricnode"></a>FabricNode

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|ClientAuthX509FindType |cadeia, padrão é "FindByThumbprint" |Dinâmica|Indica como procurar certificado na loja especificado pelo ClienteAuthX509StoreName Valor suportado: FindByThumbprint; FindBySubjectName. |
|ClienteAuthX509FindValue |cadeia, padrão é "" | Dinâmica|Valor do filtro de pesquisa utilizado para localizar o certificado para função de administração padrão FabricClient. |
|ClientAuthX509FindValueSecondary |cadeia, padrão é "" |Dinâmica|Valor do filtro de pesquisa utilizado para localizar o certificado para função de administração padrão FabricClient. |
|Nome clienteAuthX509StoreName |cadeia, padrão é "My" |Dinâmica|Nome da loja de certificados X.509 que contém certificado para função de administração padrão FabricClient. |
|ClusterX509FindType |cadeia, padrão é "FindByThumbprint" |Dinâmica|Indica como procurar certificado de cluster na loja especificado por Valores suportados pelo ClusterX509StoreName: "FindByThumbprint"; "FindBySubjectName" com "FindBySubjectName"; quando há vários fósforos; o que tem a expiração mais distante é usado. |
|ClusterX509FindValue |cadeia, padrão é "" |Dinâmica|Valor do filtro de pesquisa utilizado para localizar o certificado de cluster. |
|ClusterX509FindValueSecondary |cadeia, padrão é "" |Dinâmica|Valor do filtro de pesquisa utilizado para localizar o certificado de cluster. |
|Nome clusterx509Store |cadeia, padrão é "My" |Dinâmica|Nome da loja de certificados X.509 que contém certificado de cluster para garantir a comunicação intra-cluster. |
|EndApplicationPortRange |Int, o padrão é 0 |Estático|Fim (sem inclusão) das portas de aplicação geridas pelo subsistema de hospedagem. Necessário se EndpointFilteringEnabled for verdadeiro no Hosting. |
|ServerAuthX509FindType |cadeia, padrão é "FindByThumbprint" |Dinâmica|Indica como procurar o certificado do servidor na loja especificado por ServerAuthX509StoreName Valor suportado: FindByThumbprint; FindBySubjectName. |
|ServerAuthX509FindValue |cadeia, padrão é "" |Dinâmica|Valor do filtro de pesquisa utilizado para localizar o certificado do servidor. |
|ServerAuthX509FindValueSecondary |cadeia, padrão é "" |Dinâmica|Valor do filtro de pesquisa utilizado para localizar o certificado do servidor. |
|Nome da loja ServerAuthX509 |cadeia, padrão é "My" |Dinâmica|Nome da loja de certificados X.509 que contém certificado de servidor para serviço de entrada. |
|StartApplicationPortRange |Int, o padrão é 0 |Estático|Início das portas de aplicação geridas através do subsistema de hospedagem. Necessário se EndpointFilteringEnabled for verdadeiro no Hosting. |
|StateTraceInterval |Tempo em segundos, o padrão é 300 |Estático|Especifique a timepan em segundos. O intervalo para rastrear o estado do nó em cada nó e até os nós em FM/FMM. |
|UserRoleClientX509FindType |cadeia, padrão é "FindByThumbprint" |Dinâmica|Indica como procurar certificado na loja especificado pelo UtilizadorRoleClientX509StoreName Valor suportado: FindByThumbprint; FindBySubjectName. |
|UserRoleClientX509FindValue |cadeia, padrão é "" |Dinâmica|Valor do filtro de pesquisa utilizado para localizar o certificado para a função de utilizador padrão FabricClient. |
|UserRoleClientX509FindValueSecondary |cadeia, padrão é "" |Dinâmica|Valor do filtro de pesquisa utilizado para localizar o certificado para a função de utilizador padrão FabricClient. |
|Nome userroleClientX509StoreName |cadeia, padrão é "My" |Dinâmica|Nome da loja de certificados X.509 que contém certificado para a função de utilizador padrão FabricClient. |

## <a name="failovermanager"></a>FailoverManager

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|AllowNodeStateRemovedForSeedNode|Bool, o padrão é FALSO |Dinâmica|Bandeira para indicar se é permitido remover o estado do nó para um nó de sementes |
|BuildReplicaTimeLimit|TimeSpan, o padrão é Comum::TimeSpan::FromSeconds(3600)|Dinâmica|Especifique a timepan em segundos. O prazo para a construção de uma réplica imponente; após o qual um relatório de saúde de aviso será iniciado |
|ClusterPauseThreshold|int, o padrão é 1|Dinâmica|Se o número de nós no sistema for inferior a este valor, então a colocação; equilíbrio de carga; e o failover é interrompido. |
|CreateInstanceTimeLimit|TimeSpan, o padrão é Comum::TimeSpan::FromSeconds(300)|Dinâmica|Especifique a timepan em segundos. O prazo para a criação de um caso apátrida; após o qual um relatório de saúde de aviso será iniciado |
|EsperadoClustersize|int, o padrão é 1|Dinâmica|Quando o cluster é inicialmente iniciado; o FM esperará que tantos nós se apresentem antes de começar a colocar outros serviços; incluindo os serviços de sistema como o nome. Aumentar este valor aumenta o tempo que um cluster leva para iniciar; mas impede que os nós iniciais fiquem sobrecarregados e também os movimentos adicionais que serão necessários à medida que mais nós entram online. Este valor deve geralmente ser definido para uma pequena fração do tamanho inicial do cluster. |
|Esperada Interrupção de NadeDuration|TimeSpan, o predefinição é Comum::TimeSpan::FromSeconds (60.0 \* 30)|Dinâmica|Especifique a timepan em segundos. Esta é a duração esperada para um nó completar a desativação. |
|Esperado NodeFabricUpgradeDuration|TimeSpan, o predefinição é Comum::TimeSpan::FromSeconds (60.0 \* 30)|Dinâmica|Especifique a timepan em segundos. Esta é a duração esperada para um nó ser atualizado durante a atualização do Windows Fabric. |
|EsperadaReplicaUpgradeDuration|TimeSpan, o predefinição é Comum::TimeSpan::FromSeconds (60.0 \* 30)|Dinâmica|Especifique a timepan em segundos. Esta é a duração esperada para que todas as réplicas sejam atualizadas num nó durante a atualização da aplicação. |
|IsSingletonReplicaMoveAllowedDuringUpgrade|bool, o padrão é VERDADEIRO|Dinâmica|Se definido como verdadeiro; réplicas com um tamanho definido de réplica de 1 alvo serão permitidas a mover-se durante a atualização. |
|MinReplicaSetsize|int, o padrão é 3|Não é permitido|Este é o tamanho mínimo de réplica definida para o FM. Se o número de réplicas de FM ativas descer abaixo deste valor; o FM rejeitará alterações ao cluster até que pelo menos o número de min de réplicas seja recuperado |
|ColocaçãoConstratas|cadeia, padrão é ""|Não é permitido|Quaisquer restrições de colocação para as réplicas do gestor de failover |
|Estágio de ColocaçãoLimit|TimeSpan, o padrão é Comum::TimeSpan::FromSeconds(600)|Dinâmica|Especifique a timepan em segundos. O prazo limite para atingir a contagem de réplicas do alvo; após o qual um relatório de saúde de aviso será iniciado |
|QurumLossWaitDuration |Tempo em segundos, o padrão é MaxValue |Dinâmica|Especifique a timepan em segundos. Esta é a duração máxima para a qual permitimos que uma partição esteja num estado de perda de quórum. Se a partição ainda estiver em perda de quórum após esta duração; a partição é recuperada da perda de quórum considerando as réplicas para baixo como perdidas. Note que isto pode potencialmente incorrer na perda de dados. |
|ReconfiguraçãoTimeLimit|TimeSpan, o padrão é Comum::TimeSpan::FromSeconds(300)|Dinâmica|Especifique a timepan em segundos. O prazo para a reconfiguração; após o qual um relatório de saúde de aviso será iniciado |
|ReplicaRestartWaitDuration|TimeSpan, o predefinição é Comum::TimeSpan::FromSeconds (60.0 \* 30)|Não é permitido|Especifique a timepan em segundos. Esta é a ReplicaRestartWaitDuration para o FMService |
| SeedNodeQuorumAdditionalBufferNodes | int, o padrão é 0 | Dinâmica | Tampão de nós de sementes que é necessário estar em cima (juntamente com o quórum dos nós de sementes) FM deve permitir que um máximo de (totalNumSeedNodes - (seedNodeQuorum + SeedNodeQuorumAdditionalBufferNodes)) os nós de sementes desçam. |
|StandByReplicaKeepDuration|Timepan, o predefinição é Comum::TimeSpan::FromSeconds (3600.0 \* 24 \* 7)|Não é permitido|Especifique a timepan em segundos. Este é o StandByReplicaKeepDuration para o FMService |
|TargetReplicaSetsize|int, o padrão é 7|Não é permitido|Este é o número-alvo de réplicas fm que o Windows Fabric irá manter. Um número mais elevado resulta numa maior fiabilidade dos dados de FM; com uma pequena troca de desempenho. |
|UserMaxStandByReplicaCount |Int, o padrão é 1 |Dinâmica|O número máximo predefinido de réplicas standBy que o sistema mantém para os serviços do utilizador. |
|UserReplicarestartWaitDuration |Tempo em segundos, o padrão é 60.0 \* 30 |Dinâmica|Especifique a timepan em segundos. Quando uma réplica persistida cai; O Windows Fabric aguarda esta duração para que a réplica volte a ser apresentada antes de criar novas réplicas de substituição (o que exigiria uma cópia do estado). |
|UserStandByReplicaKeepDuration |Tempo em segundos, o padrão é 3600.0 \* 24 \* 7 |Dinâmica|Especifique a timepan em segundos. Quando uma réplica persistida volta de um estado baixo; pode já ter sido substituído. Este temporizador determina quanto tempo o FM manterá a réplica de espera antes de a descartar. |

## <a name="faultanalysisservice"></a>FaultAnalysisService

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|CompletedActionKeepDurationInSeconds | Int, o padrão é 604800 |Estático| Este é aproximadamente o tempo para manter as ações que estão em estado terminal. Isto também depende do StoredActionCleanupIntervalInSeconds; uma vez que o trabalho de limpeza só é feito nesse intervalo. 604800 é 7 dias. |
|DataLossCheckPollIntervalInSeconds|int, o padrão é 5|Estático|Este é o tempo entre as verificações que o sistema realiza enquanto espera que a perda de dados aconteça. O número de vezes que o número de perda de dados será verificado por iteração interna é DataLossCheckWaitDurationInSeconds/this. |
|DataLossCheckWaitDurationInSeconds|int, o padrão é 25|Estático|A quantidade total de tempo; em segundos; que o sistema vai esperar que a perda de dados aconteça. Isto é usado internamente quando o StartPartitionDataLossAsync() api é chamado. |
|MinReplicaSetsize |Int, o padrão é 0 |Estático|O MinReplicaSetSize para FaultAnalysisService. |
|ColocaçãoConstratas | cadeia, padrão é ""|Estático| Os Estados-Maiores de Colocação para FaultAnalysisService. |
|QurumLossWaitDuration | Tempo em segundos, o padrão é MaxValue |Estático|Especifique a timepan em segundos. O QuorumLossWaitDuration for FaultAnalysisService. |
|ReplicaDropWaitDurationInSeconds|int, o padrão é 600|Estático|Este parâmetro é usado quando a api de perda de dados é chamada. Controla quanto tempo o sistema espera que uma réplica seja largada depois de a réplica remover ser invocada internamente. |
|ReplicaRestartWaitDuration |Tempo em segundos, o padrão é de 60 minutos|Estático|Especifique a timepan em segundos. The ReplicaRestartWaitDuration for FaultAnalysisService. |
|StandByReplicaKeepDuration| Tempo em segundos, o padrão é (60*24*7) minutos |Estático|Especifique a timepan em segundos. O StandByReplicaKeepDuration for FaultAnalysisService. |
|StoredActionCleanupIntervalInSeconds | Int, o padrão é 3600 |Estático|Esta é a frequência com que a loja será limpa. Apenas ações num estado terminal; e que concluído pelo menos CompletedActionKeepDurationInSegundos ago será removido. |
|StoredChaosEventCleanupIntervalInSeconds | Int, o padrão é 3600 |Estático|Esta é a frequência com que a loja será auditada para limpeza; se o número de eventos for superior a 30000; a limpeza vai fazer efeito. |
|TargetReplicaSetsize |Int, o padrão é 0 |Estático|NOT_PLATFORM_UNIX_START o TargetReplicaSetSize para FaultAnalysisService. |

## <a name="federation"></a>de Federação

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|ArrendamentoDuração |Tempo em segundos, o padrão é 30 |Dinâmica|Duração que um arrendamento dura entre um nó e seus vizinhos. |
|ArrendamentoDurationAcrossFaultDomain |Tempo em segundos, o padrão é 30 |Dinâmica|Duração que um arrendamento dura entre um nó e seus vizinhos através de domínios de falhas. |

## <a name="filestoreservice"></a>Serviço de Loja de Arquivos

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|AceitarChunkUpload|Bool, o padrão é VERDADEIRO|Dinâmica|Config para determinar se o serviço de loja de ficheiros aceita ou não o upload de ficheiros baseado em pedaços durante o pacote de aplicação de cópia. |
|AnónimoAccessEnabled | Bool, o padrão é verdadeiro |Estático|Ativar/Desativar o acesso anónimo às ações do FileStoreService. |
|Nome Comum1Ntlmx509CommonName|cadeia, padrão é ""|Estático| O nome comum do certificado X509 utilizado para gerar HMAC no CommonName1NtlmPasswordSecret ao utilizar a autenticação NTLM |
|CommonName1Ntlmx509StoreLocation|cadeia, padrão é "LocalMachine"|Estático|A localização da loja do certificado X509 utilizado para gerar HMAC no CommonName1NtlmPasswordSecret ao utilizar a autenticação NTLM |
|Nome Comum1Ntlmx509StoreName|cadeia, padrão é "MY"| Estático|O nome da loja do certificado X509 utilizado para gerar HMAC no CommonName1NtlmPasswordSecret ao utilizar a autenticação NTLM |
|Nome Comum2Ntlmx509CommonName|cadeia, padrão é ""|Estático|O nome comum do certificado X509 utilizado para gerar HMAC no CommonName2NtlmPasswordSecret ao utilizar a autenticação NTLM |
|CommonName2Ntlmx509StoreLocation|cadeia, padrão é "LocalMachine"| Estático|A localização da loja do certificado X509 utilizado para gerar HMAC no CommonName2NtlmPasswordSecret ao utilizar a autenticação NTLM |
|Nome Comum2Ntlmx509StoreName|cadeia, padrão é "MY"|Estático| O nome da loja do certificado X509 utilizado para gerar HMAC no CommonName2NtlmPasswordSecret ao utilizar a autenticação NTLM |
|CommonNameNtlmPasswordSecret|SecureString, o padrão é Comum::SecureString("")| Estático|O segredo da palavra-passe que usou como semente para gerar a mesma senha ao usar a autenticação NTLM |
|DiskSpaceHealthReportingIntervalWhenCloseToOutOfDiskSpace |TimeSpan, o padrão é Comum::TimeSpan::FromMinutes(5)|Dinâmica|Especifique a timepan em segundos. O intervalo de tempo entre a verificação do espaço do disco para reportar eventos de saúde quando o disco está perto do espaço. |
|DiskSpaceHealthReportingIntervalWhenEnoughDiskSpace |TimeSpan, o padrão é Comum::TimeSpan::FromMinutes (15)|Dinâmica|Especifique a timepan em segundos. O intervalo de tempo entre a verificação do espaço do disco para reportar eventos de saúde quando há espaço suficiente no disco. |
|EnableImageStoreHealthReporting |bool, o padrão é VERDADEIRO    |Estático|Config para determinar se o serviço de loja de arquivos deve reportar a sua saúde. |
|FreeDiskSpaceNotificationSizeInKB|int64, padrão é 25 \* 1024 |Dinâmica|O tamanho do espaço livre do disco abaixo do qual pode ocorrer um aviso sanitário. O valor mínimo deste config e freeDiskSpaceNotificationThresholdpercentage config são usados para determinar o envio do aviso de saúde. |
|FreeDiskSpaceNotificationThresholdPercentage|duplo, padrão é 0.02 |Dinâmica|A percentagem de espaço livre em disco abaixo do qual pode ocorrer um aviso sanitário. O valor mínimo deste config e freeDiskSpaceNotificationInMB config são usados para determinar o envio de aviso de saúde. |
|Gerev1CommonNameAccount| bool, o padrão é VERDADEIRO|Estático|Especifica se deve gerar uma conta com o algoritmo de geração V1 do nome de utilizador. Começando pela versão 6.1 do Tecido de Serviço; uma conta com a geração V2 é sempre criada. A conta V1 é necessária para atualizações de/para versões que não suportam a geração V2 (antes de 6.1).|
|MaxCopyOperationThreads | Uint, o padrão é 0 |Dinâmica| O número máximo de ficheiros paralelos que o secundário pode copiar do primário. '0' == número de núcleos. |
|MaxFileOperationThreads | Uint, o padrão é 100 |Estático| O número máximo de fios paralelos permitiu a realização de Operações de Ficheiro (Copy/Move) na primária. '0' == número de núcleos. |
|MaxRequestProcessingThreads | Uint, o padrão é 200 |Estático|O número máximo de fios paralelos permitiu processar pedidos na primária. '0' == número de núcleos. |
|MaxSecondaryFileCopyFailureThreshold | Uint, o padrão é 25|Dinâmica|O número máximo de cópias de ficheiros retrós em secundário antes de desistir. |
|MaxStoreOperations | Uint, o padrão é 4096 |Estático|O número máximo de operações de transação de lojas paralelas permitidas no primário. '0' == número de núcleos. |
|NamingOperationTimeout |Tempo em segundos, o padrão é 60 |Dinâmica|Especifique a timepan em segundos. O tempo limite para a operação de nomeação. |
|PrimaryAccountNTLMPasswordSecret | SecureString, o padrão está vazio |Estático| O segredo da palavra-passe que usou como semente para gerar a mesma palavra-passe ao utilizar a autenticação NTLM. |
|PrimaryAccountNTLMX509StoreLocation | cadeia, padrão é "LocalMachine"|Estático| A localização da loja do certificado X509 utilizado para gerar HMAC no PrimaryAccountNTLMPasswordSecret ao utilizar a autenticação NTLM. |
|Número de loja PrimaryAccountNTLMX509Store | cadeia, padrão é "MY"|Estático| O nome da loja do certificado X509 utilizado para gerar HMAC no PrimaryAccountNTLMPasswordSecret ao utilizar a autenticação NTLM. |
|Contagem primáriaNTLMX509Thumbprint | cadeia, padrão é ""|Estático|A impressão digital do certificado X509 utilizado para gerar HMAC no PrimaryAccountNTLMPasswordSecret ao utilizar a autenticação NTLM. |
|Principais ResultadosType | cadeia, padrão é "" |Estático|A conta primáriaType do principal para a ACL as ações do FileStoreService. |
|Nome principal do Número de Contagem | cadeia, padrão é "" |Estático|O nome de utilizador da conta principal da ACL ações do FileStoreService. |
|Palavra-passe PrimaryAccountUser | SecureString, o padrão está vazio |Estático|A palavra-passe da conta principal do principal para a ACL que o FileStoreService partilha. |
|ConsultaOperationTimeout | Tempo em segundos, o padrão é 60 |Dinâmica|Especifique a timepan em segundos. O tempo limite para a realização de consultas. |
|Contagem secundáriaNTLMPasswordSecret | SecureString, o padrão está vazio |Estático| O segredo da palavra-passe que usou como semente para gerar a mesma palavra-passe ao utilizar a autenticação NTLM. |
|Bolsa secundáriaNTLMX509StoreLocation | cadeia, padrão é "LocalMachine" |Estático|A localização da loja do certificado X509 utilizado para gerar HMAC no SecondaryAccountNTLMPasswordSecret ao utilizar a autenticação NTLM. |
|Número de lojas SecondaryAccountNTLMX509store | cadeia, padrão é "MY" |Estático|O nome da loja do certificado X509 utilizado para gerar HMAC no SecondaryAccountNTLMPasswordSecret ao utilizar a autenticação NTLM. |
|Contagem secundáriaNTLMX509Thumbprint | cadeia, padrão é ""| Estático|A impressão digital do certificado X509 utilizado para gerar HMAC no SecondaryAccountNTLMPasswordSecret ao utilizar a autenticação NTLM. |
|SecundárioSAccountType | cadeia, padrão é ""|Estático| OTipo de Conta Secundária do principal da ACL que o FileStoreService partilha. |
|Nome secundário do Número de Professores | cadeia, padrão é ""| Estático|O nome de utilizador da conta secundária do principal para a ACL ações do FileStoreService. |
|Palavra de passagem do Segundo Comissariado | SecureString, o padrão está vazio |Estático|A senha de conta secundária do principal para a ACL que o FileStoreService partilha. |
|Ficheiros SecundáriosCopyRetryDelayMilliseconds|uint, o padrão é 500|Dinâmica|O atraso da cópia do ficheiro (em milissegundos).|
|UseChunkContentInTransportMessage|bool, o padrão é VERDADEIRO|Dinâmica|A bandeira para a utilização da nova versão do protocolo de upload introduzido em v6.4. Esta versão protocolar utiliza o transporte de tecido de serviço para carregar ficheiros para a loja de imagens, o que proporciona um melhor desempenho do que o protocolo SMB utilizado em versões anteriores. |

## <a name="healthmanager"></a>HealthManager

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|Ativar a AplicaçãoTypeHealthEvaluation |Bool, o padrão é falso. |Estático|Política de avaliação da saúde do cluster: permitir por avaliação de saúde tipo de aplicação. |
|MaxSuggestedNumberOfEntityHealthReports|Int, o padrão é 100 |Dinâmica|O número máximo de relatórios de saúde que uma entidade pode ter antes de levantar preocupações sobre a lógica de relatórios de saúde do cão de guarda. Cada entidade de saúde deve ter um número relativamente pequeno de relatórios de saúde. Se a contagem de relatórios for superior a este número; pode haver problemas com a implementação do cão de guarda. Uma entidade com muitos relatórios é sinalizada através de um relatório de saúde de advertência quando a entidade é avaliada. |

## <a name="healthmanagerclusterhealthpolicy"></a>HealthManager/ClusterHealthPolicy

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|ConsidereWarningAsError |Bool, o padrão é falso. |Estático|Política de avaliação da saúde do cluster: as advertências são tratadas como erros. |
|MaxPercentUnhealthyApplicações | Int, o padrão é 0 |Estático|Política de avaliação da saúde do cluster: o máximo por cento das aplicações não saudáveis permitiu que o cluster fosse saudável. |
|MaxPercentUnhealthyNodes | Int, o padrão é 0 |Estático|Política de avaliação da saúde do cluster: o máximo por cento dos nós não saudáveis permitiu que o cluster fosse saudável. |

## <a name="healthmanagerclusterupgradehealthpolicy"></a>HealthManager/ClusterUpgradeHealthPolicy

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|MaxPercentDeltaUnhealthyNodes|int, o padrão é 10|Estático|Política de avaliação de saúde de upgrade do cluster: percentagem máxima de nóns não saudáveis delta permitiu que o cluster fosse saudável |
|MaxPercentUpgradeDomainDeltaUnhealthyNodes|int, o padrão é 15|Estático|Política de avaliação de saúde de upgrade do cluster: percentagem máxima de delta de nódoas não saudáveis num domínio de upgrade permitiu que o cluster fosse saudável |

## <a name="hosting"></a>Hosting

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|ActivationMaxFailureCount |Número inteiro, padrão é 10 |Dinâmica|Número de vezes que as retríssias do sistema falharam na ativação antes de desistirem |
|ActivationMaxRetryInterval |Tempo em segundos, o padrão é 300 |Dinâmica|Em todas as falhas de ativação contínuas, o sistema retrindo a ativação para até ativationMaxFailureCount. ActivationMaxRetryInterval especifica intervalo de tempo de espera antes de voltar a tentar após cada falha de ativação |
|ActivationRetryBackoffInterval |Tempo em Segundos, o padrão é 5 |Dinâmica|Intervalo de recuo em todas as falhas de ativação; Em todas as falhas de ativação contínuas, o sistema retrindo a ativação para até ao MaxActivationFailureCount. O intervalo de repetição em cada tentativa é um produto de falha de ativação contínua e do intervalo de recuo da ativação. |
|ActivationTimeout| TimeSpan, o padrão é Comum::TimeSpan::FromSeconds(180)|Dinâmica| Especifique a timepan em segundos. O tempo limite para a ativação da aplicação; desativação e atualização. |
|ApplicationHostCloseTimeout| TimeSpan, o padrão é Comum::TimeSpan::FromSeconds(120)|Dinâmica| Especifique a timepan em segundos. Quando a saída do tecido é detetada num processo auto-activado; O FabricRuntime fecha todas as réplicas no processo de anfitrião do utilizador (anfitrião de aplicações). Este é o tempo limite para a operação de encerramento. |
| CnsNetworkPluginCnmUrlPort | wstring, padrão é L"48080" | Estático | Porta url Azure cnm api |
| CnsNetworkPluginCnsUrlPort | wstring, padrão é L"10090" | Estático | Porta url do Azure CNS |
|Contentores|cadeia, padrão é "-H localhost:2375 -H npipe://"|Estático|O Service Fabric (SF) gere o daemon do docker (exceto em máquinas de clientes de janelas como o Win10). Esta configuração permite ao utilizador especificar argumentos personalizados que devem ser passados para o daemon docker ao iniciá-lo. Quando os argumentos personalizados são especificados, o Tecido de Serviço não passa qualquer outro argumento para o motor Docker, exceto o argumento "-pidfile". Por conseguinte, os utilizadores não devem especificar o argumento "-pidfile" como parte dos argumentos dos seus clientes. Além disso, os argumentos personalizados devem garantir que o Docker Daemon ouve no tubo de nome predefinido no Windows (ou tomada de domínio Unix no Linux) para que o Tecido de Serviço possa comunicar com ele.|
|ContainerServiceLogFileMaxSizeInKb|int, o padrão é 32768|Estático|Tamanho máximo do ficheiro de ficheiro de registo gerado por recipientes de estivadores.  Só janelas.|
|ContainerImageDownloadTimeout|int, número de segundos, o padrão é de 1200 (20 minutos)|Dinâmica|Número de segundos antes do download dos tempos de imagem.|
|ContainerImagesToSkip|cadeia, nomes de imagem separados por caracteres de linha vertical, padrão é ""|Estático|Nome de uma ou mais imagens de recipiente que não devem ser apagadas.  Usado com o parâmetro PruneContainerImages.|
|ContainerServiceLogFileNamePrefixo|cadeia, padrão é "sfcontainerlogs"|Estático|Prefixo de nome de ficheiro para ficheiros de registo gerados por contentores de estivadores.  Só janelas.|
|ContainerServiceLogFileRetentionCount|int, o padrão é 10|Estático|Número de ficheiros de registo gerados por contentores de estivadores antes de os ficheiros de registo serem substituídos.  Só janelas.|
|CreateFabricRuntimeTimeout|TimeSpan, o padrão é Comum::TimeSpan::FromSeconds(120)|Dinâmica| Especifique a timepan em segundos. O valor de tempo limite para a chamada sync FabricCreateRuntime |
|DefaultContainerRepositoryAccountName|cadeia, padrão é ""|Estático|Credenciais padrão utilizadas em vez de credenciais especificadas em ApplicationManifest.xml |
|PredefiniçãoContainerRepositoryPassword|cadeia, padrão é ""|Estático|Credenciais de senha padrão utilizadas em vez de credenciais especificadas em ApplicationManifest.xml|
|DefaultContainerRepositoryPasswordType|cadeia, padrão é ""|Estático|Quando não estiver em cadeia vazia, o valor pode ser "Encriptado" ou "SecretsStoreRef".|
|DefaultDnsSearchS sufixoEmpty|bool, o padrão é FALSO|Estático|Por predefinição, o nome de serviço é anexado ao nome SF DNS para serviços de contentores. Esta funcionalidade impede este comportamento de modo a que nada seja anexado ao nome DNS SF por padrão na via de resolução.|
|ImplementaçãoMaxFailureCount|int, o padrão é 20| Dinâmica|A implementação da aplicação será novamente julgada para o DeploymentMaxFailureCount vezes antes de falhar a implementação dessa aplicação no nó.| 
|ImplementaçãoMaxRetryInterval| TimeSpan, o padrão é Comum::TimeSpan::FromSeconds(3600)|Dinâmica| Especifique a timepan em segundos. Intervalo de retíria máximo para a colocação. Em cada falha contínua, o intervalo de repetição é calculado como Min(DeploymentMaxRetryInterval; Contagem de falhas contínuas * DeploymentRetryBackoffInterval) |
|ImplementaçãoRetryBackoffInterval| TimeSpan, o padrão é Comum::TimeSpan::FromSeconds(10)|Dinâmica|Especifique a timepan em segundos. Intervalo de retenção para a falha de implantação. Em todas as falhas de implementação contínua, o sistema tentará novamente a implementação até ao MaxDeploymentFailureCount. O intervalo de retenção é um produto de falha de implantação contínua e do intervalo de desdobramento. |
|DisableContainers|bool, o padrão é FALSO|Estático|Config para recipientes desativados - usado em vez de DisableContainerServiceStartOnContainerActivatorOpen que é depreciado config |
|DisableDockerRequestRetry|bool, o padrão é FALSO |Dinâmica| Por defeito, a SF comunica com dD (docker dameon) com um tempo limite de 'DockerRequestTimeout' para cada pedido http enviado para o mesmo. Se o DD não responder dentro deste período de tempo; A SF reencaminho o pedido se a operação de nível superior ainda tiver tempo restante.  Com recipiente hiperv; DD às vezes demora muito mais tempo a trazer o recipiente ou desativá-lo. Nestes casos, o DD solicita tempos fora do ponto de vista da SF e SF retrição a operação. Às vezes isto parece aumentar a pressão sobre o DD. Este config permite desativar esta nova agem e esperar que o DD responda. |
|DnsServerListTwoIps | Bool, o padrão é FALSO | Estático | Esta bandeira adiciona o servidor dns local duas vezes para ajudar a aliviar problemas de resolução intermitente. |
| DoNotInjectLocalDnsServer | bool, o padrão é FALSO | Estático | Evita que o tempo de execução injete o IP local como servidor DNS para contentores. |
|EnableActivateNoWindow| bool, o padrão é FALSO|Dinâmica| O processo ativado é criado em segundo plano sem qualquer consola. |
|EnableContainerServiceDebugMode|bool, o padrão é VERDADEIRO|Estático|Ativar/desativar a sessão para recipientes de estivadores.  Só janelas.|
|EnableDockerHealthCheckIntegration|bool, o padrão é VERDADEIRO|Estático|Permite a integração de eventos docker HEALTHCHECK com relatório de saúde do sistema de tecido de serviço |
|Ativar o ProcessamentoDepuring|bool, o padrão é FALSO|Dinâmica| Permite o lançamento de anfitriões de aplicações sob depurar |
|EndpointProviderEnabled| bool, o padrão é FALSO|Estático| Permite a gestão dos recursos endpoint pela Fabric. Requer especificação da gama de portas de aplicação inicial e final no FabricNode. |
|FabricContainerAppsEnabled| bool, o padrão é FALSO|Estático| |
|FirewallPolicyEnabled|bool, o padrão é FALSO|Estático| Permite a abertura de portas de firewall para recursos Endpoint com portas explícitas especificadas no ServiceManifest |
|GetCodePackageActivationContextTimeout|TimeSpan, o padrão é Comum::TimeSpan::FromSeconds(120)|Dinâmica|Especifique a timepan em segundos. O valor de tempo limite para as chamadas CodePackageActivationContext. Isto não é aplicável aos serviços ad hoc. |
|GovernOnlyMainMemoryForProcesses|bool, o padrão é FALSO|Estático|O comportamento predefinido da Governação dos Recursos consiste em colocar o limite especificado no MemoryInMB sobre a quantidade total de memória (RAM + swap) que o processo utiliza. Se o limite for ultrapassado; o processo receberá exceção OutOfMemory. Se este parâmetro for definido como verdadeiro; o limite será aplicado apenas à quantidade de memória RAM que um processo irá utilizar. Se este limite for ultrapassado; e se esta configuração for verdadeira; em seguida, o SO trocará a memória principal pelo disco. |
|IPProviderEnabled|bool, o padrão é FALSO|Estático|Permite a gestão de endereços IP. |
|IsDefaultContainerRepositoryPasswordEncrypted|bool, o padrão é FALSO|Estático|Se a palavra DefaultContainerRepositoryPass é encriptada ou não.|
|LinuxExternalExecutablePath|cadeia, padrão é "/usr/bin/" |Estático|O diretório primário de comandos execuíveis externos no nó.|
|NTLMAuthenticationEnabled|bool, o padrão é FALSO|Estático| Permite o suporte para a utilização do NTLM pelos pacotes de código que estão a funcionar como outros utilizadores para que os processos através de máquinas possam comunicar de forma segura. |
|NTLMAuthenticationPasswordSecret|SecureString, o padrão é Comum::SecureString("")|Estático|É um tem um sistema encriptado que é usado para gerar a palavra-passe para utilizadores NTLM. Tem de ser definido se a NTLMAuthenticationEnabled for verdadeira. Validado pelo desdobrador. |
|NTLMSecurityUsersByX509CommonNamesRefreshInterval|TimeSpan, o padrão é Comum::TimeSpan::FromMinutes(3)|Dinâmica|Especifique a timepan em segundos. Definições específicas do ambiente O intervalo periódico em que o alojamento verifica novos certificados a utilizar para a configuração do FileStoreService NTLM. |
|NTLMSecurityUsersByX509CommonNamesRefreshTimeout|TimeSpan, o padrão é Comum::TimeSpan::FromMinutes(4)|Dinâmica| Especifique a timepan em segundos. O tempo limite para configurar utilizadores NTLM usando nomes comuns de certificado. Os utilizadores NTLM são necessários para ações FileStoreService. |
|PúneContainerImages|bool, o padrão é FALSO|Dinâmica| Remova as imagens do recipiente de aplicação não usado dos nós. Quando um ApplicationType não estiver registado no cluster de Tecido de Serviço, as imagens de contentores que foram utilizadas por esta aplicação serão removidas nos nós onde foi descarregada pela Service Fabric. A poda é de cada hora, pelo que pode demorar até uma hora (mais tempo para podar a imagem) para que as imagens sejam removidas do cluster.<br>O Service Fabric nunca descarregará ou removerá imagens não relacionadas com uma aplicação.  Imagens não relacionadas que foram descarregadas manualmente ou de outra forma devem ser removidas explicitamente.<br>As imagens que não devem ser eliminadas podem ser especificadas no parâmetro ContainerImagesToSkip.| 
|RegisterCodePackageHostTimeout|TimeSpan, o padrão é Comum::TimeSpan::FromSeconds(120)|Dinâmica| Especifique a timepan em segundos. O valor de tempo limite para a chamada de sincronização FabricRegisterCodePackagest. Isto é aplicável apenas para anfitriões de pacotes de vários códigos como FWP |
|RequestTimeout|TimeSpan, o padrão é Comum::TimeSpan::FromSeconds(30)|Dinâmica| Especifique a timepan em segundos. Isto representa o tempo limite de comunicação entre o anfitrião de aplicações do utilizador e o processo de Fabric para várias operações relacionadas com o alojamento, tais como o registo de fábrica; registo de tempo de execução. |
|RunAsPolicyEnabled| bool, o padrão é FALSO|Estático| Permite a execução de pacotes de código como utilizador local que não seja o utilizador em que o processo de tecido está em execução. Para permitir esta política, o Tecido deve estar a funcionar como SYSTEM ou como utilizador que tenha o SeAssignPrimaryTomaryPrivilege. |
|ServiceFactoryRegistrationTimeout| TimeSpan, o padrão é Comum::TimeSpan::FromSeconds(120)|Dinâmica|Especifique a timepan em segundos. O valor de tempo limite para a chamada sync Register (Stateless/Stateful)ServiceFactory |
|ServiceTypeDisableFailureThreshold |Número inteiro, padrão é 1 |Dinâmica|Este é o limiar para a contagem de falhas após o qual FailoverManager (FM) é notificado para desativar o tipo de serviço nesse nó e tentar um nó diferente para colocação. |
|ServiceTypeDisableGraceInterval|TimeSpan, o padrão é Comum::TimeSpan::FromSeconds(30)|Dinâmica|Especifique a timepan em segundos. Intervalo de tempo após o qual o tipo de serviço pode ser desativado |
|ServiçoTypeRegistrationTimeout |Tempo em Segundos, o padrão é 300 |Dinâmica|Tempo máximo permitido para o ServiceType ser registado com tecido |
|UseContainerServiceArguments|bool, o padrão é VERDADEIRO|Estático|Este config diz ao anfitrião para saltar argumentos de passagem (especificados em config ContainerServiceArments) para estivar daemon.|

## <a name="httpgateway"></a>HttpGateway

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|ActiveListeners |Uint, o padrão é 50 |Estático| Número de leituras para publicar na fila do servidor http. Isto controla o número de pedidos simultâneos que podem ser satisfeitos pelo HttpGateway. |
|httpGatewayHealthReportsendInterval |Tempo em segundos, o padrão é 30 |Estático|Especifique a timepan em segundos. O intervalo em que o Portal http envia relatórios de saúde acumulados ao Health Manager. |
|HttpStrictTransportSecurityHeader|cadeia,padrão é ""|Dinâmica| Especifique o valor do cabeçalho de segurança de transporte rigoroso HTTP a incluir em todas as respostas enviadas pelo HttpGateway. Quando definido para a corda vazia; este cabeçalho não será incluído na resposta gateway.|
|IsEnabled|Bool, o padrão é falso. |Estático| Ativa/Desativa o HttpGateway. HttpGateway é desativado por padrão. |
|MaxentityBodySize |Uint, o padrão é 4194304 |Dinâmica|Dá o tamanho máximo do corpo que pode ser esperado a partir de um pedido http. O valor predefinido é de 4MB. Httpgateway falhará um pedido se tiver um corpo de tamanho > este valor. O tamanho mínimo do pedaço de leitura é de 4096 bytes. Portanto, isto tem de ser >= 4096. |

## <a name="imagestoreservice"></a>ImageStoreService

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|Ativado |Bool, o padrão é falso. |Estático|A bandeira ativada para ImageStoreService. Padrão: falso |
|MinReplicaSetsize | Int, o padrão é 3 |Estático|O MinReplicaSetSize para ImageStoreService. |
|ColocaçãoConstratas | cadeia, padrão é "" |Estático| Os PlacementConstraints para ImageStoreService. |
|QurumLossWaitDuration | Tempo em segundos, o padrão é MaxValue |Estático| Especifique a timepan em segundos. O QuorumLossWaitDuration for ImageStoreService. |
|ReplicaRestartWaitDuration | Tempo em segundos, o padrão é 60.0 \* 30 |Estático|Especifique a timepan em segundos. The ReplicaRestartWaitDuration for ImageStoreService. |
|StandByReplicaKeepDuration | Tempo em segundos, o padrão é 3600.0 \* 2 |Estático| Especifique a timepan em segundos. O StandByReplicaKeepDuration for ImageStoreService. |
|TargetReplicaSetsize | Int, o padrão é 7 |Estático|O TargetReplicaSetSize para ImageStoreService. |

## <a name="ktllogger"></a>KtlLogger

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|Configuração automática |Int, o padrão é 1 |Dinâmica|Bandeira que indica se as definições de memória devem ser configuradas automaticamente e dinamicamente. Se zero, as definições de configuração da memória são utilizadas diretamente e não mudam com base nas condições do sistema. Se uma, então, as definições de memória forem configuradas automaticamente e podem ser alteradas com base nas condições do sistema. |
|Máximo DedestagingWriteOutstandingInKB | Int, o padrão é 0 |Dinâmica|O número de KB para permitir que o registo partilhado avance antes do registo dedicado. Use 0 para indicar nenhum limite.
|SharedLogId |cadeia, padrão é "" |Estático|Guia único para recipiente de log compartilhado. Utilize "" se utilizar o caminho predefinido sob a raiz de dados de tecido. |
|CompartilhadoLogAta |cadeia, padrão é "" |Estático|Caminho e nome de arquivo para o local para colocar o recipiente de log compartilhado. Utilize "" para utilizar o caminho predefinido sob a raiz de dados de tecido. |
|SharedLogSizeInMB |Int, o padrão é 8192 |Estático|O número de MB a atribuir no contentor de tronco partilhado. |
|SharedLogThrottleLimitInPercentUsed|int, o padrão é 0 | Estático | A percentagem de utilização do registo partilhado que irá induzir o estrangulamento. O valor deve estar entre 0 e 100. Um valor de 0 implica a utilização do valor percentual predefinido. Um valor de 100 não implica qualquer estrangulamento. Um valor entre 1 e 99 especifica a percentagem de utilização do registo acima da qual ocorrerá estrangulamento; por exemplo, se o registo partilhado for de 10GB e o valor for de 90, então o estrangulamento ocorrerá quando 9GB estiver em uso. Recomenda-se a utilização do valor predefinido.|
|WriteBufferMemoryPoolMaximumInKB | Int, o padrão é 0 |Dinâmica|O número de KB para permitir que o conjunto de memórias de tampão de escrita cresça. Use 0 para indicar nenhum limite. |
|WriteBufferMemoryPoolMinimumInKB |Int, o padrão é 8388608 |Dinâmica|O número de KB a atribuir inicialmente para o conjunto de memória tampão de escrita. A utilização 0 para indicar que o Padrão de Não Limite deve ser consistente com o SharedLogSizeInMB abaixo. |

## <a name="managedidentitytokenservice"></a>ManagedIdentityTokenService
| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|IsEnabled|bool, o padrão é FALSO|Estático|O controlo da presença e do estado do Serviço de Token de Identidade Gerida no cluster;este é um pré-requisito para a utilização da funcionalidade de identidade gerida das aplicações de Tecido de Serviço.|

## <a name="management"></a>Gestão

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|AutomaticamenteUnprovisionInterval|TimeSpan, o padrão é Comum::TimeSpan::FromMinutes(5)|Dinâmica|Especifique a timepan em segundos. O intervalo de limpeza para o tipo de aplicação não registado durante a limpeza automática do tipo de aplicação.|
|AzureStorageMaxConnections | Int, o padrão é 5000 |Dinâmica|O número máximo de ligações simultâneas ao armazenamento azul. |
|AzureStorageMaxWorkerThreads | Int, o padrão é 25 |Dinâmica|O número máximo de fios de trabalhador em paralelo. |
|AzureStorageOperationTimeout | Tempo em segundos, o padrão é 6000 |Dinâmica|Especifique a timepan em segundos. Tempo para a operação xstore completar. |
|CleanupApplicationPackageOnProvisionSuccess|bool, o padrão é FALSO |Dinâmica|Permite ou desativa a limpeza automática do pacote de aplicações com uma oferta bem sucedida.

*A melhor prática é `true` usar.* | | CleanupUnusedApplicationTypes Bool, o padrão é FALSO / Dinâmico Esta configuração, se ativada, permite automaticamente versões de tipo de aplicação nãoregudas, ignorando as três versões mais recentes não reutilizadas, aparando assim o espaço do disco ocupado pela loja de imagens. A limpeza automática será desencadeada no final da provisão bem sucedida para esse tipo de aplicação específica e também é executado periodicamente uma vez por dia para todos os tipos de aplicações. O número de versões não utilizados a saltar é configurável utilizando o parâmetro "MaxUnusedAppTypeVersionsToKeep". 

*A melhor prática é `true` usar.*
| | DisableChecksumValidation / Bool, o padrão é falso. Estática Esta configuração permite-nos ativar ou desativar a validação do checksum durante o provisionamento da aplicação. | | DisableServerSideCopy Bool, o padrão é falso. Estática Esta configuração permite ou desativa a cópia do pacote de aplicações na ImageStore durante o provisionamento da aplicação. | | ImageCachingEnabled [ ImageCachingEnabled ] Bool, o padrão é verdadeiro. Estática Esta configuração permite-nos ativar ou desativar o caching. | | ImageStoreConnectionString / SecureString Estática Cadeia de ligação à Raiz para a ImageStore. | | ImageStoreMinimumTransferBPS Int, o padrão é 1024 / Dinâmico A taxa mínima de transferência entre o cluster e a ImageStore. Este valor é utilizado para determinar o tempo limite de acesso à ImageStore externa. Altere este valor apenas se a latência entre o cluster e a ImageStore for elevada para permitir que o cluster descarregue a partir da ImageStore externa. | | MaxUnusedAppTypeVersionsTo Manutenção Int, o padrão é 3 / Dinâmico Esta configuração define o número de versões do tipo de aplicação não reutilizadas a serem ignoradas para limpeza. Este parâmetro só é aplicável se o parâmetro CleanupUnusedApplicationTypes estiver ativado.

*As melhores práticas gerais são utilizar o padrão `3` ().*|


## <a name="metricactivitythresholds"></a>MétricaActivityThresholds
| **Parâmetro** | **Valores Permitidos** |**Política de Upgrade**| **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|Grupo de Propriedades|KeyIntegerValueMap, padrão é Nenhum|Dinâmica|Determina o conjunto de MetricActivityThresholds para as métricas do cluster. O equilíbrio funcionará se o maxNodeLoad for maior do que o MetricActivityThresholds. Para métricas de defrag define a quantidade de carga igual ou abaixo que o Tecido de Serviço considerará o nó vazio |

## <a name="metricbalancingthresholds"></a>MetricBalancingThresholds
| **Parâmetro** | **Valores Permitidos** |**Política de Upgrade**| **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|Grupo de Propriedades|KeyDoubleValueMap, padrão é nenhum|Dinâmica|Determina o conjunto de MetricBalancingThresholds para as métricas do cluster. O equilíbrio funcionará se maxNodeLoad/minNodeLoad for maior do que o MetricBalancingThresholds. A desfragmentação funcionará se maxNodeLoad/minNodeLoad em pelo menos um FD ou UD for menor do que a MetricBalancingThresholds. |

## <a name="metricloadstickinessforswap"></a>MetricLoadStickinessForSwap
| **Parâmetro** | **Valores Permitidos** |**Política de Upgrade**| **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|Grupo de Propriedades|KeyDoubleValueMap, padrão é nenhum|Dinâmica|Determina a parte da carga que fica com a réplica quando trocada Tem valor entre 0 (a carga não fica com a réplica) e 1 (paus de carga com réplica - predefinido) |

## <a name="namingservice"></a>Serviço de Nomeação

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|GatewayServiceDescriptionCacheLimit |Int, o padrão é 0 |Estático|O número máximo de entradas mantidas na cache de descrição do serviço LRU no Gateway de Nomeação (definido para 0 sem limite). |
|MaxClientConnections |Int, o padrão é 1000 |Dinâmica|O número máximo permitido de ligações de clientes por gateway. |
|MaxFileOperationTimeout |Tempo em segundos, o padrão é 30 |Dinâmica|Especifique a timepan em segundos. O tempo limite máximo permitido para a operação de serviço de loja de ficheiros. Os pedidos que especificarem um tempo limite maior serão rejeitados. |
|MaxIndexedEmptyPartitions |Int, o padrão é 1000 |Dinâmica|O número máximo de divisórias vazias que permanecerão indexadas na cache de notificação para sincronizar clientes de reconectação. Quaisquer divisórias vazias acima deste número serão removidas do índice na ordem de versão de procura ascendente. Reconectar clientes ainda pode sincronizar e receber atualizações de partição vazias perdidas; mas o protocolo de sincronização torna-se mais caro. |
|MaxMessageSize |Int, o padrão é 4 \* 1024 \* 1024 |Estático|O tamanho máximo da mensagem para a comunicação do nó do cliente ao usar o nome. Alívio do ataque dos DOS; o valor padrão é de 4MB. |
|MaxNamingServiceHealthReports | Int, o padrão é 10 |Dinâmica|O número máximo de operações lentas que o serviço de loja naming reporta pouco saudável de uma só vez. Se 0; todas as operações lentas são enviadas. |
|MaxOperationTimeout |Tempo em segundos, o padrão é 600 |Dinâmica|Especifique a timepan em segundos. O tempo máximo permitido para as operações do cliente. Os pedidos que especificarem um tempo limite maior serão rejeitados. |
|MaxOutstandingNotificationsPerClient |Int, o padrão é 1000 |Dinâmica|O número máximo de notificações pendentes antes de um registo de cliente é fechado à força pelo portal. |
|MinReplicaSetsize | Int, o padrão é 3 |Não é permitido| O número mínimo de réplicas do Serviço de Nomeação necessárias para escrever para completar uma atualização. Se houver menos réplicas do que esta ativa no sistema, o Sistema de Fiabilidade nega atualizações para a Loja de Serviços de Nomeação até que as réplicas sejam restauradas. Este valor nunca deve ser mais do que o TargetReplicaSetSize. |
|PartitionCount |Int, o padrão é 3 |Não é permitido|O número de divisórias da loja Naming Service a criar. Cada partição possui uma única chave de partição que corresponde ao seu índice; chaves de partição tão [0; O Conde de Divisões existe. Aumentar o número de divisórias do Serviço de Nomeação aumenta a escala que o Serviço de Nomeação pode realizar diminuindo a quantidade média de dados detidos por qualquer conjunto de réplicas de suporte; a um custo de maior utilização de recursos (uma vez que as réplicas de serviço PartitionCount*ReplicaSetSize devem ser mantidas).|
|ColocaçãoConstratas | cadeia, padrão é "" |Não é permitido| Restrição de colocação para o Serviço de Nomeação. |
|QurumLossWaitDuration | Tempo em segundos, o padrão é MaxValue |Não é permitido| Especifique a timepan em segundos. Quando um Serviço de Nomeação entra em perda de quórum; este temporizador começa. Quando expirar, o FM considerará as réplicas de down como perdidas; e tentar recuperar o quórum. Não que isto possa resultar em perda de dados. |
|ReparaçãoInterval | Tempo em segundos, o padrão é 5 |Estático| Especifique a timepan em segundos. Intervalo em que será iniciada a reparação da inconsistência de nomeação entre o proprietário da autoridade e o titular do nome. |
|ReplicaRestartWaitDuration | Tempo em segundos, o padrão é (60.0 * 30)|Não é permitido| Especifique a timepan em segundos. Quando uma réplica do Serviço de Nomeação se apaga; este temporizador começa. Quando expirar, o FM começará a substituir as réplicas que estão em baixo (ainda não as considera perdidas). |
|ServiçoDesscriçãoCacheLimit | Int, o padrão é 0 |Estático| O número máximo de entradas mantidas na cache de descrição do serviço LRU no Serviço de Loja de Nomeação (definido para 0 sem limite). |
|ServiceNotificationTimeout |Tempo em segundos, o padrão é 30 |Dinâmica|Especifique a timepan em segundos. O tempo limite utilizado na entrega de notificações de serviço ao cliente. |
|StandByReplicaKeepDuration | Tempo em segundos, o padrão é 3600.0 * 2 |Não é permitido| Especifique a timepan em segundos. Quando uma réplica do Serviço de Nomeação volta de um estado baixo; pode já ter sido substituído. Este temporizador determina quanto tempo o FM manterá a réplica de espera antes de a descartar. |
|TargetReplicaSetsize |Int, o padrão é 7 |Não é permitido|O número de conjuntos de réplicas para cada partição da loja Naming Service. Aumentar o número de conjuntos de réplicas aumenta o nível de fiabilidade das informações na Loja de Serviços de Nomeação; diminuição da alteração que a informação perder-se-á em consequência de falhas nos nódois; com um custo de carga aumentada no Windows Fabric e o tempo que leva para executar atualizações para os dados de nomeação.|

## <a name="nodebufferpercentage"></a>NodeBufferPercentage
| **Parâmetro** | **Valores Permitidos** |**Política de Upgrade**| **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|Grupo de Propriedades|KeyDoubleValueMap, padrão é nenhum|Dinâmica|Percentagem de capacidade do nó por nome métrico; usado como tampão, a fim de manter algum lugar livre em um nó para o caso de failover. |

## <a name="nodecapacities"></a>NodeCapacities

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|Grupo de Propriedades |NodeCapacityCollectionMap |Estático|Uma coleção de capacidades de nó para diferentes métricas. |

## <a name="nodedomainids"></a>NodeDomainIds

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|Grupo de Propriedades |NodeFaultDomainIdCollection |Estático|Descreve os domínios de avaria a que pertence um nó. O domínio da falha é definido através de um URI que descreve a localização do nó no datacenter.  Os URIs do Domínio da Falha são do formato fd:/fd/ seguidos por um segmento de trajetória URI.|
|UpgradeDomainId |cadeia, padrão é "" |Estático|Descreve o domínio de atualização a que pertence um nó. |

## <a name="nodeproperties"></a>Não-dedepropertas

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|Grupo de Propriedades |NodePropertyCollectionMap |Estático|Uma coleção de pares de valores-chave de corda para propriedades de nó. |

## <a name="paas"></a>Rio Paas

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|ClusterId |cadeia, padrão é "" |Não é permitido|Loja de certificados X509 utilizada por tecido para proteção de configuração. |

## <a name="performancecounterlocalstore"></a>PerformanceCounterLocalStore

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|Contador |Cadeia | Dinâmica |Lista separada por vírgula de contadores de desempenho para recolher. |
|IsEnabled |Bool, o padrão é verdadeiro | Dinâmica |A bandeira indica se a recolha do contador de desempenho no nó local está ativada. |
|MaxCounterBinaryFileSizeInMB |Int, o padrão é 1 | Dinâmica |Tamanho máximo (em MB) para cada ficheiro binário do contador de desempenho. |
|NewCounterBinaryFileCreationIntervalInMinutes |Int, o padrão é 10 | Dinâmica |Intervalo máximo (em segundos) após o qual é criado um novo ficheiro binário de contador de desempenho. |
|AmostragemIntervalInSeconds |Int, o padrão é 60 | Dinâmica |Intervalo de amostragem para os contadores de desempenho que estão a ser recolhidos. |

## <a name="placementandloadbalancing"></a>PlacementAndLoadBalancing

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|AffinityConstraintPriority | Int, o padrão é 0 | Dinâmica|Determina a prioridade da restrição de afinidade: 0: Difícil; 1: Macio; negativo: Ignorar. |
|Capacidade de AplicaçãoConstratePrioridade | Int, o padrão é 0 | Dinâmica|Determina a prioridade da restrição de capacidade: 0: Difícil; 1: Macio; negativo: Ignorar. |
|AutoDetectAvailableResources|bool, o padrão é VERDADEIRO|Estático|Este config irá desencadear a deteção automática dos recursos disponíveis no nó (CPU e Memória) Quando este config estiver definido como verdadeiro - vamos ler capacidades reais e corrigi-las se o utilizador especificar capacidades de nó mau ou não os definir de todo Se esta config estiver definida como falsa - vamos rastrear um aviso de que o utilizador especificou capacidades de nó mau; mas não vamos corrigi-los; o que significa que o utilizador quer ter as capacidades especificadas como > do que o nó realmente tem ou se as capacidades são indefinidas; assumirá capacidade ilimitada |
|BalanceDelayAfterNewNode | Tempo em segundos, o padrão é 120 |Dinâmica|Especifique a timepan em segundos. Não comece a equilibrar as atividades dentro deste período após a adição de um novo nó. |
|BalancingDelayAfterNodeDown | Tempo em segundos, o padrão é 120 |Dinâmica|Especifique a timepan em segundos. Não comece a equilibrar as atividades dentro deste período após um evento de nó para baixo. |
|CapacidadeConstrataPrioridade | Int, o padrão é 0 | Dinâmica|Determina a prioridade da restrição de capacidade: 0: Difícil; 1: Macio; negativo: Ignorar. |
|Gotas consecutivasMovementsHealthReportLimit | Int, o padrão é 20 | Dinâmica|Define o número de vezes consecutivas que os movimentos emitidos pelo ResourceBalancer são eliminados antes de serem realizados os diagnósticos e emitidos avisos sanitários. Negativo: Não são emitidos avisos nesta condição. |
|RestriçãoFixPartialDelayAfterNewNode | Tempo em segundos, o padrão é 120 |Dinâmica| Especifique a timepan em segundos. DDo não corrige falhas Demômína e UpgradeDomain violações de restrições dentro deste período após a adição de um novo nó. |
|RestriçãoFixPartialDelayAfterNodeDown | Tempo em segundos, o padrão é 120 |Dinâmica| Especifique a timepan em segundos. Não corrija as violações de restrições de FaultDomain e UpgradeDomain dentro deste período após um evento de nó para baixo. |
|RestriçãoViolationHealthReportLimit | Int, o padrão é 50 |Dinâmica| Define o número de vezes que a restrição de violação da réplica tem de ser persistentemente não corrigida antes de serem realizados os diagnósticos e os relatórios de saúde são emitidos. |
|DetalhadoConstraintViolationHealthReportLimit | Int, o padrão é 200 |Dinâmica| Define o número de vezes que a restrição de violação da réplica tem de ser persistentemente não corrigida antes de serem realizados os diagnósticos e são emitidos relatórios de saúde detalhados. |
|DetalhadoDiagnosticsInfoListLimit | Int, o padrão é 15 |Dinâmica| Define o número de entradas de diagnóstico (com informação detalhada) por restrição a incluir antes da truncação em Diagnósticos.|
|Detalhado NodeListLimit | Int, o padrão é 15 |Dinâmica| Define o número de nós por restrição a incluir antes da truncação nos relatórios de réplica não colocados. |
|DetalhadoPartitionListLimit | Int, o padrão é 15 |Dinâmica| Define o número de divisórias por entrada de diagnóstico para uma restrição a incluir antes da truncação em Diagnósticos. |
|DetalhadoVerboseHealthReportLimit | Int, o padrão é 200 | Dinâmica|Define o número de vezes que uma réplica não colocada tem de ser persistentemente não colocada antes de serem emitidos relatórios de saúde detalhados. |
|Executação Das CapacidadesMétricas do Serviço De Serviço|bool, o padrão é FALSO | Estático |Permite a proteção de serviços de tecido. Todos os serviços de utilizador estão sob um objeto de trabalho/grupo e limitados a uma quantidade especificada de recursos. Isto precisa de ser estático (requer o reinício do FabricHost) como criação/remoção do objeto de trabalho do utilizador e definição de limites em estojo durante a abertura do Hospedeiro de Tecido. |
|FaultDomainConstraintPriority | Int, o padrão é 0 |Dinâmica| Determina a prioridade da restrição do domínio da falha: 0: Difícil; 1: Macio; negativo: Ignorar. |
|GlobalMovementThrottleCountingInterval | Tempo em segundos, o padrão é 600 |Estático| Especifique a timepan em segundos. Indicar o comprimento do intervalo anterior para o qual rastrear por movimentos de réplica de domínio (usado juntamente com GlobalMovementThrottleThreshold). Pode ser definido para 0 para ignorar completamente o estrangulamento global. |
|GlobalMovementThrottleThreshold | Uint, o padrão é 1000 |Dinâmica| Número máximo de movimentos permitidos na Fase de Equilíbrio no intervalo anterior indicado pela GlobalMovementThrottleCountingInterval. |
|GlobalMovementThrottleThresholdForBalancing | Uint, o padrão é 0 | Dinâmica|Número máximo de movimentos permitidos na Fase de Equilíbrio no intervalo anterior indicado pela GlobalMovementThrottleCountingInterval. 0 indica que não há limite. |
|GlobalMovementThrottleThresholdForPlacement | Uint, o padrão é 0 |Dinâmica| O número máximo de movimentos permitidos na Fase de Colocação no intervalo anterior indicado pela GlobalMovementThrottleCountingInterval.0 não indica nenhum limite.|
|GlobalMovementThrottleThresholdPercentage|duplo, padrão é 0|Dinâmica|Número máximo de movimentos totais permitidos nas fases de Equilíbrio e Colocação (expressa em percentagem do número total de réplicas no cluster) no intervalo anterior indicado pela GlobalMovementThrottleCountingInterval. 0 indica que não há limite. Se este e o GlobalMovementThrottleThreshold forem especificados; então é usado um limite mais conservador.|
|GlobalMovementThrottleThresholdPercentageForBalancing|duplo, padrão é 0|Dinâmica|Número máximo de movimentos permitidos na Fase de Equilíbrio (expressa em percentagem do número total de réplicas em PLB) no intervalo anterior indicado pela GlobalMovementThrottleCountingInterval. 0 indica que não há limite. Se este e o GlobalMovementThrottleThresholdForBalancing forem especificados; então é usado um limite mais conservador.|
|InBuildThrottlingAssociatedMetric | cadeia, padrão é "" |Estático| O nome métrico associado para este estrangulamento. |
|InBuildThrottlingEnabled | Bool, o padrão é falso. |Dinâmica| Determine se o estrangulamento em construção está ativado. |
|InBuildThrottlingGlobalMaxValue | Int, o padrão é 0 |Dinâmica|O número máximo de réplicas em construção permitidas globalmente. |
|InterrupçãoBalancingForAllFailoverUpdates | Bool, o padrão é falso. | Dinâmica|Determina se qualquer tipo de atualização da unidade de failover deve interromper o funcionaamento rápido ou lento do equilíbrio. Com o processo de equilíbrio "falso" especificado, será interrompido se o FailoverUnit: for criado/eliminado; tem réplicas em falta; alterou a localização da réplica primária ou alterou o número de réplicas. O balanceamento não será interrompido noutros casos - se o FailoverUnit: tiver réplicas extra; mudou qualquer bandeira de réplica; mudou apenas a versão de partição ou qualquer outro caso. |
|MinConstraintCheckInterval | Tempo em segundos, o padrão é 1 |Dinâmica| Especifique a timepan em segundos. Define o tempo mínimo que deve passar antes de duas rondas de verificação de restrições consecutivas. |
|MinLoadBalancingInterval | Tempo em segundos, o padrão é 5 |Dinâmica| Especifique a timepan em segundos. Define o tempo mínimo que deve passar antes de duas rondas de equilíbrio consecutivas. |
|MinPlacementInterval | Tempo em segundos, o padrão é 1 |Dinâmica| Especifique a timepan em segundos. Define o tempo mínimo que deve passar antes de duas rondas de colocação consecutivas. |
|MoveExistingReplicaForPlacement | Bool, o padrão é verdadeiro |Dinâmica|Definição que determina se move a réplica existente durante a colocação. |
|MovementPerPartitionThrottleCountingInterval | Tempo em segundos, o padrão é 600 |Estático| Especifique a timepan em segundos. Indicar o comprimento do intervalo anterior para o qual rastrear os movimentos de réplica para cada partição (utilizado juntamente com MovementPerPartitionThrottleThreshold). |
|MovementPerPartitionThrottleThreshold | Uint, o padrão é 50 |Dinâmica| Não ocorrerá nenhum movimento relacionado com o equilíbrio para uma partição se o número de movimentos relacionados de equilíbrio para réplicas dessa partição tiver atingido ou excedido o MovementPerFailoverUnitThrottle Detenha no intervalo anterior indicado pelo MovementPerPartitionThrottleCountingInterval. |
|MoveParentToFixAffinityViolation | Bool, o padrão é falso. |Dinâmica| Definição que determina se as réplicas dos pais podem ser movidas para corrigir restrições de afinidade.|
|Serviços ParcialmentePlace | Bool, o padrão é verdadeiro |Dinâmica| Determina se todas as réplicas de serviço no cluster serão colocadas "tudo ou nada" dado os nós adequados limitados para eles.|
|PlaceChildWithoutParent | Bool, o padrão é verdadeiro | Dinâmica|Definição que determina se a réplica do serviço infantil pode ser colocada se não houver uma réplica dos pais. |
|ColocaçãoConstrateprioridade | Int, o padrão é 0 | Dinâmica|Determina a prioridade da restrição de colocação: 0: Difícil; 1: Macio; negativo: Ignorar. |
|PlacementConstraintValidationCachesize | Int, o padrão é 10000 |Dinâmica| Limita o tamanho da tabela utilizada para validação rápida e caching de Expressões de Restrição de Colocação. |
|PlacementSSearchTimeout | Tempo em segundos, o padrão é 0.5 |Dinâmica| Especifique a timepan em segundos. Ao colocar serviços; procurar no máximo este muito antes de devolver um resultado. |
|PLBRefreshGap | Tempo em segundos, o padrão é 1 |Dinâmica| Especifique a timepan em segundos. Define o tempo mínimo que deve passar antes que o PLB volte a refrescar o estado. |
|PreferênciaLocationConstraintPriority | Int, o padrão é 2| Dinâmica|Determina a prioridade da restrição de localização preferida: 0: Difícil; 1: Macio; 2: Otimização; negativo: Ignorar |
|PreferencialPrimaryDomainsConstraintPriority| Int, o padrão é 1 | Dinâmica| Determina a prioridade da restrição de domínio primário preferencial: 0: Difícil; 1: Macio; negativo: Ignorar |
|PreferencialUpgradedUDs|bool,padrão é FALSO|Dinâmica|Liga e desliga a lógica que prefere mudar-se para UDs já melhoradas. Começando com SF 7.0, o valor predefinido para este parâmetro é alterado de TRUE para FALSO.|
|PreventTransientOvercommit | Bool, o padrão é falso. | Dinâmica|Determina se o PLB deve contar imediatamente com recursos que serão libertados pelos movimentos iniciados. Por predefinição; O PLB pode iniciar a mudança e avançar no mesmo nó que pode criar um excesso de compromisso transitório. Definir este parâmetro verdadeiro evitará que este tipo de compromissos excessivos e desativação a pedido (aka placementWithMove) sejam desativadas. |
|EscalaoutCountConstraintPriority | Int, o padrão é 0 |Dinâmica| Determina a prioridade da restrição da contagem de escala: 0: Difícil; 1: Macio; negativo: Ignorar. |
|SubclusteringEnabled|Bool, o padrão é FALSO | Dinâmica |Reconhecer a subclusão no cálculo do desvio padrão para o equilíbrio |
|SubclusteringReportingPolicy| Int, o padrão é 1 |Dinâmica|Define como e se os relatórios de saúde subclusivas são enviados: 0: Não reportar; 1: Aviso; 2: OK |
|SwapPrimaryThrottlingAssociatedMetric | cadeia, padrão é ""|Estático| O nome métrico associado para este estrangulamento. |
|SwapPrimaryThrottlingEnabled | Bool, o padrão é falso.|Dinâmica| Determine se o estrangulamento primário de swap está ativado. |
|SwapPrimaryThrottlingGlobalMaxValue | Int, o padrão é 0 |Dinâmica| O número máximo de réplicas primárias de swap permitidas globalmente. |
|TraceCRMReasons |Bool, o padrão é verdadeiro |Dinâmica|Especifica se deve rastrear razões para o CRM ter emitido movimentos para o canal de eventos operacionais. |
|UpgradeDomainConstraintPriority | Int, o padrão é 1| Dinâmica|Determina a prioridade da restrição de domínio de upgrade: 0: Difícil; 1: Macio; negativo: Ignorar. |
|UseMoveCostReports | Bool, o padrão é falso. | Dinâmica|Instrui o LB a ignorar o elemento de custo da função de pontuação; resultando potencialmente grande número de movimentos para uma colocação mais equilibrada. |
|UtilizaçãoSeparateSecondaryLoad | Bool, o padrão é verdadeiro | Dinâmica|Regulação que determina se deve ser utilizada uma carga separada para réplicas secundárias. |
|UtilizaçãoSeparateSecondaryMoveCost | Bool, o padrão é falso. | Dinâmica|Definição que determina se o custo de movimento separado deve ser usado para réplicas secundárias. |
|Validar SubstituiçãoConstrata | Bool, o padrão é verdadeiro |Dinâmica| Especifica se a expressão PlacementConstraint para um serviço é validada quando a Subscrição de Serviço de um serviço é atualizada. |
|Validar SubstituiçãoPrimaryConstraintOnPromote| Bool, o padrão é VERDADEIRO |Dinâmica|Especifica se a expressão PlacementConstraint para um serviço é avaliada para preferência primária no failover. |
|VerboseHealthReportLimit | Int, o padrão é 20 | Dinâmica|Define o número de vezes que uma réplica tem de ficar desatada antes de ser reportada uma advertência de saúde (se estiver ativado o relatório de saúde verboso). |
|NodeLoadsOperaçãotracingEnabled | Bool, o padrão é verdadeiro |Dinâmica|Config que permite rastreios estruturais operacionais no nó carga na loja de eventos. |
|NodeLoadsOperaçãoTracingInterval | TimeSpan, o padrão é Comum::TimeSpan::FromSeconds(20) | Dinâmica|Especifique a timepan em segundos. O intervalo com o qual rastrear cargas de nó para armazenar eventos para cada domínio de serviço. |

## <a name="reconfigurationagent"></a>Reconfiguração

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|AplicaçãoUpgradeMaxReplicaCloseDuration | Tempo em segundos, o padrão é 900 |Dinâmica|Especifique a timepan em segundos. A duração pela qual o sistema aguardará antes de terminar os anfitriões de serviço que tenham réplicas que estão presas durante a Atualização de Aplicação.|
|FabricUpgradeMaxReplicaCloseDuration | Tempo em segundos, o padrão é 900 |Dinâmica| Especifique a timepan em segundos. A duração pela qual o sistema aguardará antes de terminar os anfitriões de serviço que tenham réplicas que estão presas durante a atualização do tecido. |
|GraciosaReplicaShutdownMaxDuration|TimeSpan, o padrão é Comum::TimeSpan::FromSeconds(120)|Dinâmica|Especifique a timepan em segundos. A duração pela qual o sistema aguardará antes de terminar os anfitriões de serviço que tenham réplicas que estão presas por perto. Se este valor for definido para 0, as réplicas não serão instruídas a fechar.|
|NodeDeactivaçãoMaxReplicaCloseDuration | Tempo em segundos, o padrão é 900 |Dinâmica|Especifique a timepan em segundos. A duração pela qual o sistema aguardará antes de terminar os anfitriões de serviço que tenham réplicas que estão presas durante a desativação do nó. |
|PeriodicApiSlowTraceInterval | Tempo em segundos, o padrão é de 5 minutos |Dinâmica| Especifique a timepan em segundos. PeriodicApiSlowTraceInterval define o intervalo sobre o qual as chamadas lentas da API serão retraídas pelo monitor da API. |
|ReplicaChangeRoleFailureRestartThreshold|int, o padrão é 10|Dinâmica| Inteiro inteiro. Especificar o número de falhas de API durante a promoção primária após a qual será aplicada uma ação de auto-mitigação (reinício de réplica). |
|ReplicaChangeRoleFailureWarningReportThreshold|int, o padrão é 2147483647|Dinâmica| Inteiro inteiro. Especificar o número de falhas de API durante a promoção primária após o qual o relatório de saúde de aviso será aumentado.|
|ServiceApiHealthDuration | Tempo em segundos, o padrão é de 30 minutos |Dinâmica| Especifique a timepan em segundos. ServiceApiHealthDuration define quanto tempo esperamos que um serviço API seja executado antes de reportarmos que não é saudável. |
|ServiceReconfigurationApiHealthDuration | Tempo em segundos, o padrão é 30 |Dinâmica| Especifique a timepan em segundos. ServiceReconfigurationApiHealthDuration define quanto tempo esperamos que um serviço API seja executado antes de reportarmos insalubre. Isto aplica-se às chamadas API que têm disponibilidade de impacto.|

## <a name="replication"></a>Replicação
| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade**| **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|BatchAckAckNownowledgementInterval|TimeSpan, o padrão é Comum::TimeSpan::FromMilliseconds(15)|Estático|Especifique a timepan em segundos. Determina o tempo que o replicador espera depois de receber uma operação antes de enviar de volta um aviso. Outras operações recebidas durante este período de tempo terão os seus agradecimentos enviados de volta numa única mensagem-> reduzindo o tráfego de rede, mas potencialmente reduzindo a produção do replicador.|
|MaxCopyQueueSize|uint, padrão é 1024|Estático|Este é o valor máximo que define o tamanho inicial para a fila que mantém as operações de replicação. Note que deve ser um poder de 2. Se durante o tempo de funcionamento a fila crescer para esta operação de tamanho será acelerada entre os replicadores primários e secundários.|
|MaxPrimaryReplicationQueueMemorySize|uint, o padrão é 0|Estático|Este é o valor máximo da fila de replicação primária em bytes.|
|MaxPrimaryReplicationQueueSize|uint, padrão é 1024|Estático|Este é o número máximo de operações que podem existir na fila de replicação primária. Note que deve ser um poder de 2.|
|MaxReplicationMessageSize|uint, o padrão é 52428800|Estático|Tamanho máximo da mensagem das operações de replicação. O padrão é de 50MB.|
|MaxSecondaryReplicationQueueMemorySize|uint, o padrão é 0|Estático|Este é o valor máximo da fila de replicação secundária em bytes.|
|MaxSecondaryReplicationSize|uint, padrão é 2048|Estático|Este é o número máximo de operações que podem existir na fila de replicação secundária. Note que deve ser um poder de 2.|
|QueueHealthMonitoringInterval|TimeSpan, o padrão é Comum::TimeSpan::FromSeconds(30)|Estático|Especifique a timepan em segundos. Este valor determina o período de tempo utilizado pelo Replicador para monitorizar quaisquer eventos de saúde de aviso/erro nas filas de operação de replicação. Um valor de '0' desativa a monitorização da saúde |
|QueueHealthWarningAtUsagePercent|uint, o padrão é 80|Estático|Este valor determina o uso da fila de replicação (em percentagem) após o qual reportamos alerta sobre o uso elevado da fila. Fazemo-lo depois de um intervalo de graça de QueueHealthMonitoringInterval. Se o uso da fila cair abaixo desta percentagem no intervalo de graça|
|ReplicatorAddress|cadeia, padrão é "localhost:0"|Estático|O ponto final sob a forma de uma cadeia -'IP:Port' que é utilizada pelo Replicador de Tecidos windows para estabelecer ligações com outras réplicas para enviar/receber operações.|
|ReplicatorListenAddress|cadeia, padrão é "localhost:0"|Estático|O ponto final sob a forma de uma cadeia -'IP:Port' que é utilizada pelo Replicador de Tecidos windows para receber operações de outras réplicas.|
|ReplicatorPublishAddress|cadeia, padrão é "localhost:0"|Estático|O ponto final sob a forma de uma cadeia -'IP:Port' que é utilizada pelo Replicador de Tecidos windows para enviar operações para outras réplicas.|
|RetryInterval|TimeSpan, o predefinição é Comum::TimeSpan::FromSeconds(5)|Estático|Especifique a timepan em segundos. Quando uma operação é perdida ou rejeitada este temporizador determina com que frequência o replicador voltará a tentar enviar a operação.|

## <a name="resourcemonitorservice"></a>Serviço ResourceMonitor
| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade**| **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|IsEnabled|bool, o padrão é FALSO |Estático|Controla se o serviço estiver ativado no cluster ou não. |

## <a name="runas"></a>RunAs

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|RunAsAccountName |cadeia, padrão é "" |Dinâmica|Indica o nome da conta RunAs. Isto só é necessário para o tipo de conta "DomainUser" ou "ManagedServiceAccount". Valores válidos são "domain\user" ou user@domain ". |
|RunAsAccountType|cadeia, padrão é "" |Dinâmica|Indica o tipo de conta RunAs. Isto é necessário para qualquer secção RunAs Os valores válidos são "DomainUser/NetworkService/ManagedServiceAccount/LocalSystem".|
|Palavra runAsPass|cadeia, padrão é "" |Dinâmica|Indica a palavra-passe da conta RunAs. Isto só é necessário para o tipo de conta "DomainUser". |

## <a name="runas_dca"></a>RunAs_DCA

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|RunAsAccountName |cadeia, padrão é "" |Dinâmica|Indica o nome da conta RunAs. Isto só é necessário para o tipo de conta "DomainUser" ou "ManagedServiceAccount". Valores válidos são "domain\user" ou user@domain ". |
|RunAsAccountType|cadeia, padrão é "" |Dinâmica|Indica o tipo de conta RunAs. Isto é necessário para qualquer secção RunAs Os valores válidos são "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|Palavra runAsPass|cadeia, padrão é "" |Dinâmica|Indica a palavra-passe da conta RunAs. Isto só é necessário para o tipo de conta "DomainUser". |

## <a name="runas_fabric"></a>RunAs_Fabric

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|RunAsAccountName |cadeia, padrão é "" |Dinâmica|Indica o nome da conta RunAs. Isto só é necessário para o tipo de conta "DomainUser" ou "ManagedServiceAccount". Valores válidos são "domain\user" ou user@domain ". |
|RunAsAccountType|cadeia, padrão é "" |Dinâmica|Indica o tipo de conta RunAs. Isto é necessário para qualquer secção RunAs Os valores válidos são "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|Palavra runAsPass|cadeia, padrão é "" |Dinâmica|Indica a palavra-passe da conta RunAs. Isto só é necessário para o tipo de conta "DomainUser". |

## <a name="runas_httpgateway"></a>RunAs_HttpGateway

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|RunAsAccountName |cadeia, padrão é "" |Dinâmica|Indica o nome da conta RunAs. Isto só é necessário para o tipo de conta "DomainUser" ou "ManagedServiceAccount". Valores válidos são "domain\user" ou user@domain ". |
|RunAsAccountType|cadeia, padrão é "" |Dinâmica|Indica o tipo de conta RunAs. Isto é necessário para qualquer secção RunAs Os valores válidos são "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|Palavra runAsPass|cadeia, padrão é "" |Dinâmica|Indica a palavra-passe da conta RunAs. Isto só é necessário para o tipo de conta "DomainUser". |

## <a name="security"></a>Segurança
| **Parâmetro** | **Valores Permitidos** |**Política de Upgrade**| **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|AADCertEndpointFormat|cadeia, padrão é ""|Estático|Formato AAD Cert Endpoint, Azure Commercial padrão, especificado para ambientes não padrão como O Governo Azure "https: \/ /login.microsoftonline.us/ {0} /federationmetadata/2007-06/federationmetadata.xml" |
|AADClientApplicação|cadeia, padrão é ""|Estático|Nome de aplicação do cliente nativo ou ID representando clientes de tecido |
|AADClusterApplication|cadeia, padrão é ""|Estático|Nome de aplicação da Web API ou ID que representa o cluster |
|AADLoginEndpoint|cadeia, padrão é ""|Estático|AAD Login Endpoint, Azure Commercial padrão, especificado para ambientes não padrão como O Governo Azure "https: \/ /login.microsoftonline.us" |
|AADTenantId|cadeia, padrão é ""|Estático|ID do inquilino (GUID) |
|AcceptExpiredPinnedClusterCertificate|bool, o padrão é FALSO|Dinâmica|A bandeira indicando se aceita certificados de agrupamento expirados declarados por impressão digital aplica-se apenas aos certificados de agrupamento; de modo a manter o aglomerado vivo. |
|AdminClientCertThumbprints|cadeia, padrão é ""|Dinâmica|Impressões digitais de certificados utilizados pelos clientes em funções de administração. É uma lista de nomes separados por vírgulas. |
|AADTokenEndpointFormat|cadeia, padrão é ""|Estático|AAD Token Endpoint, padrão Azure Commercial, especificado para ambientes não padrão como O Governo Azure "https: \/ {0} /login.microsoftonline.us/" |
|AdminClientClaims|cadeia, padrão é ""|Dinâmica|Todas as possíveis reclamações esperadas dos clientes administrativos; o mesmo formato que o ClientClaims; esta lista é adicionada internamente ao ClienteClaims; por isso, não há necessidade de adicionar também as mesmas entradas ao ClienteClaims. |
|AdminClientIdentidades|cadeia, padrão é ""|Dinâmica|Identidades do Windows de clientes de tecidos em funções de administração; usado para autorizar operações de tecido privilegiado. É uma lista separada por vírgula; cada entrada é um nome de conta de domínio ou nome de grupo. Por conveniência; a conta que executa fabric.exe é automaticamente atribuída função de administrador; assim é o grupo ServiceFabricAdministrators. |
|AppRunAsAccountGroupX509Folder|cadeia, padrão é /home/sfuser/sfusercerts |Estático|Pasta onde estão localizados os certificados e chaves privadas do AppRunAsAccountGroup X509 |
|CertificadoExpirySafetyMargin|TimeSpan, o padrão é Comum::TimeSpan::FromMinutes(43200)|Estático|Especifique a timepan em segundos. Margem de segurança para a caducidade do certificado; certifica-se que o estado do relatório de saúde muda de OK para Aviso quando a expiração é mais próxima do que esta. O padrão é de 30 dias. |
|CertificateHealthReportingInterval|TimeSpan, o padrão é Comum::TimeSpan::FromSeconds (3600 * 8)|Estático|Especifique a timepan em segundos. Especificar intervalo para o relatório sanitário do certificado; incumprimento de 8 horas; definição para 0 desativa relatórios de saúde de certificados |
|Marcas De ClienteCertThumb|cadeia, padrão é ""|Dinâmica|Impressões digitais de certificados utilizados pelos clientes para falar com o cluster; cluster utiliza esta ligação de entrada autorizada. É uma lista de nomes separados por vírgulas. |
|ClienteClaimAuthEnabled|bool, o padrão é FALSO|Estático|Indica se a autenticação baseada em sinistros está ativada nos clientes; definindo este verdadeiro conjuntos implícitos ClientRoleEnabled. |
|ClienteClaims|cadeia, padrão é ""|Dinâmica|Todas as possíveis reclamações esperadas dos clientes para a ligação ao gateway. Esta é uma lista 'OR': ClaimsEntry \| \| \| \| ClaimsEntry ClaimsEntry ... cada ClaimsEntry é uma lista "E": ClaimType=ClaimValue && ClaimType=ClaimValue && ClaimType=ClaimValue ... |
|Clientes|cadeia, padrão é ""|Dinâmica|Identidades de Janelas de FabricClient; o portal de nomeação utiliza isto para autorizar ligações de entrada. É uma lista separada por vírgula; cada entrada é um nome de conta de domínio ou nome de grupo. Por conveniência; a conta que executa fabric.exe é automaticamente permitida; assim são os ServiceFabricAllowedUsers e ServiceFabricAdministrators. |
|ClientRoleEnabled|bool, o padrão é FALSO|Estático|Indica se a função do cliente está ativada; quando definido como verdadeiro; os clientes são atribuídos papéis com base nas suas identidades. Para V2; permitindo isto significa que o cliente que não está no AdminClientCommonNames/AdminClientIdentities só pode executar operações de leitura. |
|ClusterCertThumbprints|cadeia, padrão é ""|Dinâmica|Impressões digitais de certificados autorizados a aderir ao cluster; uma lista de nomes separados por vírgulas. |
|ClusterCredentialType|cadeia, padrão é "Nenhum"|Não é permitido|Indica o tipo de credenciais de segurança a utilizar para proteger o cluster. Valores válidos são "Nenhum/X509/Windows" |
|Clusteridentidades|cadeia, padrão é ""|Dinâmica|Identidades do Windows dos nós de cluster; usado para a autorização de adesão ao cluster. É uma lista separada por vírgula; cada entrada é um nome de conta de domínio ou nome de grupo |
|ClusterSpn|cadeia, padrão é ""|Não é permitido|Nome principal do serviço do cluster; quando o tecido funciona como um único utilizador de domínio (gMSA/conta de utilizador de domínio). É a SPN de ouvintes e ouvintes do arrendamento em fabric.exe: ouvintes da federação; ouvintes de replicação interna; ouvinte de serviço de tempo de execução e ouvinte de gateway de nomeação. Isto deve ser deixado vazio quando o tecido funciona como contas da máquina; nesse caso, ligar o ouvinte do computação lateral SPN do endereço de transporte do ouvinte. |
|CrlCheckingFlag|uint, o padrão é 0x40000000|Dinâmica|Bandeira de validação da cadeia de certificados padrão; Pode ser ultrapassado por pavilhão específico dos componentes; por exemplo, Federação/X509CertChainFlags 0x1000000000 CERT_CHAIN_REVOCATION_CHECK_END_CERT 0x2000000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN 0x400000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT 0x800000000000 CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY Configuração para 0 desativar CRL lista completa de valores suportados é documentada por dwFlags of GetCertificCha: https://msdn.microsoft.com/library/windows/desktop/aa376078(v=vs.85).aspx |
|CrlDisablePeriod|TimeSpan, o padrão é Comum::TimeSpan::FromMinutes (15)|Dinâmica|Especifique a timepan em segundos. Durante quanto tempo a verificação de CRL é desativada para um determinado certificado depois de ter encontrado um erro offline; se o erro offline do CRL puder ser ignorado. |
|CrlOfflineHealthReportTtl|TimeSpan, o padrão é Comum::TimeSpan::FromMinutes (1440)|Dinâmica|Especifique a timepan em segundos. |
|DisableFirewallRuleForDomainProfile| bool, o padrão é VERDADEIRO |Estático| Indica se a regra de firewall não deve ser ativada para o perfil de domínio |
|DisableFirewallRuleForPrivateProfile| bool, o padrão é VERDADEIRO |Estático| Indica se a regra de firewall não deve ser ativada para o perfil privado | 
|DisableFirewallRuleForPublicProfile| bool, o padrão é VERDADEIRO | Estático|Indica se a regra de firewall não deve ser ativada para o perfil público |
| EnforceLinuxMinTlsVersion | bool, o padrão é FALSO | Estático | Se definido como verdadeiro; apenas a versão TLS 1.2+ é suportada.  Se falso; apoiar versões TLS anteriores. Aplica-se apenas ao Linux |
| Impor Regras de Segurança | bool, o padrão é FALSO| Dinâmica | Controlo de bandeiras o comportamento da atualização do cluster ao detetar alterações das suas definições de segurança. Se for definido como "verdadeiro", a atualização do cluster tentará garantir que pelo menos um dos certificados correspondentes a qualquer uma das regras de apresentação possa passar uma regra de validação correspondente. A pré-validação é executada antes de as novas definições serem aplicadas a qualquer nó, mas funciona apenas no nó que hospeda a réplica primária do serviço Cluster Manager no momento de iniciar a atualização. O padrão está atualmente definido como "falso"; começando com o lançamento 7.1, a definição será definida como 'verdadeira' para novos clusters de tecido de serviço Azure.|
|FabricHostSpn| cadeia, padrão é "" |Estático| Nome principal do serviço de FabricHost; quando o tecido funciona como um único utilizador de domínio (gMSA/conta de utilizador de domínio) e fabricHost funciona sob conta de máquina. É a SPN do ouvinte do IPC para o FabricHost; que por defeito deve ser deixado vazio uma vez que FabricHost corre sob conta de máquina |
|IgnoreCrlOfflineError|bool, o padrão é FALSO|Dinâmica|Se ignorar erro offline crl quando o lado do servidor verifica certificados de clientes que chegam |
|IgnoreSvrCrlOfflineError|bool, o padrão é VERDADEIRO|Dinâmica|Se ignorar o erro offline do CRL quando o lado do cliente verifica os certificados de servidor de entrada; incumprimento à verdade. Os ataques com certificados de servidor revogados requerem DNS comprometedor; mais difícil do que com certificados de cliente revogados. |
|ServerAuthCredentialType|cadeia, padrão é "Nenhum"|Estático|Indica o tipo de credenciais de segurança a utilizar para garantir a comunicação entre o FabricClient e o Cluster. Valores válidos são "Nenhum/X509/Windows" |
|ServerCertThumbprints|cadeia, padrão é ""|Dinâmica|Impressões digitais dos certificados de servidor utilizados pelo cluster para falar com os clientes; os clientes usam isto para autenticar o cluster. É uma lista de nomes separados por vírgulas. |
|DefiniçõesX509StoreName| cadeia, padrão é "MY"| Dinâmica|Loja de certificados X509 utilizada por tecido para proteção de configuração |
|UseClusterCertForIpcServerTlsSecurity|bool, o padrão é FALSO|Estático|Se usar certificado de cluster para proteger a unidade de transporte IPC Server TLS |
|X509Folder|cadeia, padrão é /var/lib/waagent|Estático|Pasta onde estão localizados certificados X509 e chaves privadas |
|TLS1_2_CipherList| string| Estático|Se estiver definido para uma corda não vazia; substitui a lista de cifras suportada para TLS1.2 e abaixo. Consulte a documentação "openssl-ciphers" para recuperar a lista de cifras suportadas e o formato de lista Exemplo de forte lista de cifras para TLS1.2: "ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDHE-RSA-AES-128-GCM-SHA256:ECDHE-ECDSA-AES256-CBC-SHA384:ECDHE-ECDSA-AES128--CBC-SHA256:ECDHE-RSA-AES256-CBC-SHA384:ECDHE-RSA-AES128-CBC-SHA256" Aplica-se apenas ao Linux. |

## <a name="securityadminclientx509names"></a>Segurança/AdminClientX509Names

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|Grupo de Propriedades|X509NameMap, padrão é Nenhum|Dinâmica|Esta é uma lista de pares "Nome" e "Valor". Cada "Nome" é de nome comum ou DnsName de certificados X509 autorizados para operações de cliente administrativo. Para um dado "Nome", "Valor" é uma lista separada de impressões digitais de certificado para emitente que fixa, se não vazia, o emitente direto dos certificados de cliente administrativo deve estar na lista. |

## <a name="securityclientaccess"></a>Segurança/ClienteAccess

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|Ativar Node |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para ativação de um nó. |
|CancelTestCommand |cadeia, padrão é "Administrador" |Dinâmica| Cancela um TestCommand específico, se estiver em voo. |
|CodePackageControl |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para reiniciar pacotes de código. |
|Criar Aplicação |cadeia, padrão é "Administrador" | Dinâmica|Configuração de segurança para criação de aplicações. |
|CreateComposeDeployment|cadeia, padrão é "Administrador"| Dinâmica|Cria uma implementação de composição descrita por compor ficheiros |
|Criar Resource|cadeia, padrão é "Administrador"| Dinâmica|Criar um recurso de gateway |
|Criar Nome |cadeia, padrão é "Administrador" |Dinâmica|Configuração de segurança para a criação uri de nomeação. |
|Criar Rede|cadeia, padrão é "Administrador" |Dinâmica|Cria uma rede de contentores |
|Criar Serviço |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para criação de serviço. |
|Criar ServiçoFromTemplate |cadeia, padrão é "Administrador" |Dinâmica|Configuração de segurança para criação de serviço a partir do modelo. |
|CriarVolume|cadeia, padrão é "Administrador"|Dinâmica|Cria um volume |
|DesativarNode |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para desativar um nó. |
|DesactivarNodesBatch |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para desativar múltiplos nós. |
|Eliminar |cadeia, padrão é "Administrador" |Dinâmica| Configurações de segurança para o cliente da loja de imagens eliminam a operação. |
|Excluir a aplicação |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para eliminação de aplicações. |
|ExcluirComposeDeployment|cadeia, padrão é "Administrador"| Dinâmica|Elimina a implantação de composição |
|DeleteGatewayResource|cadeia, padrão é "Administrador"| Dinâmica|Elimina um recurso de gateway |
|EliminarName |cadeia, padrão é "Administrador" |Dinâmica|Configuração de segurança para a supressão uri de nomeação. |
|ExcluirNetwork|cadeia, padrão é "Administrador" |Dinâmica|Elimina uma rede de contentores |
|DeleteService |cadeia, padrão é "Administrador" |Dinâmica|Configuração de segurança para eliminação de serviço. |
|DeleteVolume|cadeia, padrão é "Administrador"|Dinâmica|Elimina um volume.| 
|Enumerações |cadeia, padrão é "Utilizador de \| \| Administração" | Dinâmica|Configuração de segurança para nomear a enumeração de propriedade. |
|EnumerasSubnames |cadeia, padrão é "Utilizador de \| \| Administração" |Dinâmica| Configuração de segurança para a enumeração uri de nomeação. |
|FileContent |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para transferência de ficheiros de cliente de loja de imagem (externa ao cluster). |
|Carga de Arquivo |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para iniciação de descarregamento de ficheiros do cliente da loja de imagens (externa ao cluster). |
|FinishInfrastructureTask |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para terminar tarefas de infraestrutura. |
|GetChaosReport | cadeia, padrão é "Utilizador de \| \| Administração" |Dinâmica| Procura o estado do Caos dentro de um determinado intervalo de tempo. |
|ObterConfiguration DeCluster | cadeia, padrão é "Utilizador de \| \| Administração" | Dinâmica|Induz a Configuração doCluster numa divisória. |
|GetClusterConfigurationUpgradeStatus | cadeia, padrão é "Utilizador de \| \| Administração" |Dinâmica| Induz GetClusterConfigurationUpgradeStatus numa partição. |
|GetFabricUpgradeStatus |cadeia, padrão é "Utilizador de \| \| Administração" |Dinâmica| Configuração de segurança para o estado de atualização do cluster de sondagens. |
|GetFolderSize |cadeia, padrão é "Administrador" |Dinâmica|Configuração de segurança para o tamanho da pasta do FileStoreService |
|GetNodeDeactivationStatus |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para verificar o estado de desativação. |
|GetNodeTransitionProgress | cadeia, padrão é "Utilizador de \| \| Administração" |Dinâmica| Configuração de segurança para obter progresso num comando de transição de nó. |
|GetPartitionDataLossProgress | cadeia, padrão é "Utilizador de \| \| Administração" | Dinâmica|Procura o progresso para uma chamada api de perda de dados. |
|GetPartitionQuorumLossProgress | cadeia, padrão é "Utilizador de \| \| Administração" |Dinâmica| Procura o progresso para uma chamada api de perda de quórum. |
|GetPartitionRestartProgress | cadeia, padrão é "Utilizador de \| \| Administração" |Dinâmica| Procura o progresso para uma chamada api de partição reiniciada. |
|GetSecrets|cadeia, padrão é "Administrador"|Dinâmica|Obtenha valores secretos |
|GetServiceDescription |cadeia, padrão é "Utilizador de \| \| Administração" |Dinâmica| Configuração de segurança para notificações de serviço de sondagem longa e descrições do serviço de leitura. |
|GetStagingLocation |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para a recuperação da localização do cliente da loja de imagens. |
|GetStoreLocation |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para a recuperação da localização da loja de clientes da loja de imagens. |
|GetUpgradeOrstrationServiceState|cadeia, padrão é "Administrador"| Dinâmica|Induz GetUpgradeOrchestrationServiceState numa partição |
|GetUpgradesPendingApproval |cadeia, padrão é "Administrador" |Dinâmica| Induz GetUpgrades GastosApproval numa partição. |
|GetUpgradeStatus |cadeia, padrão é "Utilizador de \| \| Administração" |Dinâmica| Configuração de segurança para o estado de atualização da aplicação de sondagens. |
|Lista Interna |cadeia, padrão é "Administrador" | Dinâmica|Configuração de segurança para a operação da lista de ficheiros do cliente da loja de imagens (interna). |
|InvocarContainerApi|cadeia,padrão é "Administrador"|Dinâmica|Invocar a API do contentor |
|Invocar Infraestruturas de InfraestruturasCommand |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para comandos de gestão de tarefas de infraestrutura. |
|Invocar a Infraestruturas de Agricultura |cadeia, padrão é "Utilizador de \| \| Administração" | Dinâmica|Configuração de segurança para consulta de tarefas de infraestrutura. |
|Lista |cadeia, padrão é "Utilizador de \| \| Administração" | Dinâmica|Configuração de segurança para a operação da lista de ficheiros do cliente da loja de imagens. |
|MoveNextFabricUpgradeDomain |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para retomar atualizações de cluster com um domínio de upgrade explícito. |
|MoveNextUpgradeDomain |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para retomar atualizações de aplicações com um domínio de upgrade explícito. |
|MoveReplicaControl |cadeia, padrão é "Administrador" | Dinâmica|Mover réplica. |
|NomeExists |cadeia, padrão é "Utilizador de \| \| Administração" | Dinâmica|Configuração de segurança para verificações de existência uri naming. |
|NodeControl |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para arranque; parar; e reiniciar os nós. |
|NódoastateRemoved |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para reportar o estado do nó removido. |
|Ping |cadeia, padrão é "Utilizador de \| \| Administração" |Dinâmica| Configuração de segurança para sinais de cliente. |
|Pré-deployPackageToNode |cadeia, padrão é "Administrador" |Dinâmica| Pré-desemprego api. |
|PrefixResolveService |cadeia, padrão é "Utilizador de \| \| Administração" |Dinâmica| Configuração de segurança para resolução de prefixo de serviço baseado em reclamações. |
|PropertyReadBatch |cadeia, padrão é "Utilizador de \| \| Administração" |Dinâmica| Configuração de segurança para naming operações de leitura de propriedade. |
|PropertyWriteBatch |cadeia, padrão é "Administrador" |Dinâmica|Configurações de segurança para naming operações de escrita de propriedade. |
|ProvisionApplicationType |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para provisão de tipo de aplicação. |
|ProvisionFabric |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para provisão de MSI e/ou Cluster Manifest. |
|Consulta |cadeia, padrão é "Utilizador de \| \| Administração" |Dinâmica| Configuração de segurança para consultas. |
|RecuperaçãoPartição |cadeia, padrão é "Administrador" | Dinâmica|Configuração de segurança para recuperar uma divisória. |
|RecuperaçãoPartitions |cadeia, padrão é "Administrador" | Dinâmica|Configuração de segurança para recuperar divisórias. |
|Recuperação DePartitions |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para recuperar divisórias de serviço. |
|Recuperação DePartitions Do Sistema |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para recuperar divisórias de serviço do sistema. |
|Remover Indeativações |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para reverter a desativação em múltiplos nós. |
|ReportFabricUpgradeHealth |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para retomar atualizações de cluster com o progresso atual da atualização. |
|RelatórioFault |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para reportar falha. |
|ReportHealth |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para reportar saúde. |
|ReportUpgradeHealth |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para retomar atualizações de aplicações com o progresso atual da atualização. |
|ResetPartitionLoad |cadeia, padrão é "Utilizador de \| \| Administração" |Dinâmica| Configuração de segurança para reposição da carga para uma unidade de failover. |
|ResolveNameOwner |cadeia, padrão é "Utilizador de \| \| Administração" | Dinâmica|Configuração de segurança para resolver o nome do proprietário uri. |
|ResoluçãoPartição |cadeia, padrão é "Utilizador de \| \| Administração" | Dinâmica|Configuração de segurança para resolver serviços do sistema. |
|ResolveService |cadeia, padrão é "Utilizador de \| \| Administração" |Dinâmica| Configuração de segurança para resolução de serviços baseado em reclamações. |
|ResolveSystemService|cadeia, padrão é "Utilizador de \| \| Administração"|Dinâmica| Configuração de segurança para a resolução de serviços do sistema |
|RollbackApplicationUpgrade |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para atualizações de aplicações de reversão. |
|RevésFabricUprado |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para reverter atualizações de cluster. |
|Notificação de Serviço |cadeia, padrão é "Utilizador de \| \| Administração" |Dinâmica| Configuração de segurança para notificações de serviço baseadas em eventos. |
|SetUpgradeOrchestrationServiceState|cadeia, padrão é "Administrador"| Dinâmica|Induz SetUpgradeOrchestrationServiceState numa partição |
|StartApprovedUpgrades |cadeia, padrão é "Administrador" |Dinâmica| Induz startApprovedUps numa partição. |
|StartChaos |cadeia, padrão é "Administrador" |Dinâmica| Começa o Caos - se ainda não começou. |
|StartClusterConfigurationUpgrade |cadeia, padrão é "Administrador" |Dinâmica| Induz StartClusterConfigurationUpgrade em uma divisória. |
|StartInfrastructureTask |cadeia, padrão é "Administrador" | Dinâmica|Configuração de segurança para iniciar tarefas de infraestrutura. |
|StartNodeTransition |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para iniciar uma transição de nó. |
|StartPartitionDataLoss |cadeia, padrão é "Administrador" |Dinâmica| Induz a perda de dados numa partição. |
|StartPartitionQuorumLoss |cadeia, padrão é "Administrador" |Dinâmica| Induz a perda de quórum numa partição. |
|StartPartitionRestart |cadeia, padrão é "Administrador" |Dinâmica| Simultaneamente reinicia algumas ou todas as réplicas de uma partição. |
|StopChaos |cadeia, padrão é "Administrador" |Dinâmica| Para o Caos - se já começou. |
|ToggleVerboseServicePlacementHealthReporting | cadeia, padrão é "Utilizador de \| \| Administração" |Dinâmica| Configuração de segurança para Toggling Verbose ServicePlacement HealthReporting. |
|Não-VisãoApplicationType |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para não provisão do tipo de aplicação. |
|UnprovisionFabric |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para MSI e/ou Cluster Manifest não é desprovisionamento. |
|Controlo de Transportes não confiáveis |cadeia, padrão é "Administrador" |Dinâmica| Transporte pouco fiável para adicionar e remover comportamentos. |
|Atualização Serviço |cadeia, padrão é "Administrador" |Dinâmica|Configuração de segurança para atualizações de serviço. |
|UpgradeApplication |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para iniciar ou interromper atualizações de aplicações. |
|UpgradeComposeDeployment|cadeia, padrão é "Administrador"| Dinâmica|Melhora a implantação de composição |
|UpgradeFabric |cadeia, padrão é "Administrador" |Dinâmica| Configuração de segurança para iniciar atualizações de clusters. |
|Carregar |cadeia, padrão é "Administrador" | Dinâmica|Configuração de segurança para operação de upload do cliente da loja de imagens. |

## <a name="securityclientcertificateissuerstores"></a>Segurança/ClienteCertificateIssuerStores

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|Grupo de Propriedades|EmiterStoreKeyValueMap, padrão é Nenhum |Dinâmica|Lojas de certificados de emitente X509 para certificados de cliente; Nome = clienteIssuerCN; Valor = vírgula lista separada de lojas |

## <a name="securityclientx509names"></a>Nomes de segurança/clientesX509Nam

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|Grupo de Propriedades|X509NameMap, padrão é Nenhum|Dinâmica|Esta é uma lista de pares "Nome" e "Valor". Cada "Nome" é de nome comum ou DnsName de certificados X509 autorizados para operações com cliente. Para um dado "Nome", "Valor" é uma lista separada de impressões digitais de certificado para emitente que fixa, se não vazia, o emitente direto dos certificados do cliente deve estar na lista.|

## <a name="securityclustercertificateissuerstores"></a>Segurança/ClusterCertificateIssuerStores

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|Grupo de Propriedades|EmiterStoreKeyValueMap, padrão é Nenhum |Dinâmica|Lojas de certificados de emitente X509 para certificados de cluster; Nome = clusterIssuerCN; Valor = vírgula lista separada de lojas |

## <a name="securityclusterx509names"></a>Ofendise/ClusterX509Names

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|Grupo de Propriedades|X509NameMap, padrão é Nenhum|Dinâmica|Esta é uma lista de pares "Nome" e "Valor". Cada "Nome" é de nome comum ou DnsName de certificados X509 autorizados para operações de cluster. Para um dado "Nome", "Valor" é uma lista separada de impressões digitais de certificado para emitente, se não vazia, o emitente direto dos certificados de agrupamento deve estar na lista.|

## <a name="securityservercertificateissuerstores"></a>Segurança/ServidorCertificateIssuerStores

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|Grupo de Propriedades|EmiterStoreKeyValueMap, padrão é Nenhum |Dinâmica|Lojas de certificados de emitente X509 para certificados de servidor; Nome = servidorIssuerCN; Valor = vírgula lista separada de lojas |

## <a name="securityserverx509names"></a>Nomes de segurança/ServerX509Nam

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|Grupo de Propriedades|X509NameMap, padrão é Nenhum|Dinâmica|Esta é uma lista de pares "Nome" e "Valor". Cada "Nome" é de nome comum ou DnsName de certificados X509 autorizados para operações de servidor. Para um dado "Nome", "Valor" é uma lista separada de impressões digitais de certificado para emitente, se não vazia, o emitente direto dos certificados do servidor deve estar na lista.|

## <a name="setup"></a>Configuração

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|Nome da Rede de Contentores|cadeia, padrão é ""| Estático |O nome da rede a utilizar ao configurar uma rede de contentores.|
|ContainerNetworkSetup|bool, o padrão é FALSO (Linux) e o padrão é TRUE (Windows)| Estático |Se criar uma rede de contentores.|
|FabricDataRoot |Cadeia | Não é permitido |Diretório de raiz de dados de tecido de serviço. O padrão para Azure é d:\svcfab |
|FabricLogRoot |Cadeia | Não é permitido |Diretório de raiz de tronco de tecido de serviço. É aqui que são colocados registos e vestígios de SF. |
|NódesToBeRemoved|cadeia, padrão é ""| Dinâmica |Os nós que devem ser removidos como parte da atualização da configuração. (Apenas para implantações autónomas)|
|ServiceRunAsAccountName |Cadeia | Não é permitido |O nome da conta sob o qual executar o serviço de hospeda de tecido. |
|SkipContainerNetworkResetOnReboot|bool, o padrão é FALSO|Não-alealtado|Se saltar a rede de recipientes de reposição no reboot.|
|SkipFirewallConfiguration |Bool, o padrão é falso. | Não é permitido |Especifica se as definições de firewall precisam ou não ser definidas pelo sistema. Isto só se aplica se estiver a utilizar firewall do Windows. Se estiver a utilizar firewalls de terceiros, então tem de abrir as portas para o sistema e aplicações utilizarem |

## <a name="tokenvalidationservice"></a>Serviço de Arquivação de Token

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|Fornecedores |cadeia, padrão é "DSTS" |Estático|Lista separada de comma de fornecedores de validação simbólica para permitir (fornecedores válidos são: DSTS; AAD). Atualmente, apenas um único fornecedor pode ser ativado a qualquer momento. |

## <a name="traceetw"></a>Vestígios/Etw

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|Nível |Int, o padrão é 4 | Dinâmica |O nível de etw pode levar os valores 1, 2, 3, 4. Para ser apoiado deve manter o nível de rastreada em 4 |

## <a name="transactionalreplicator"></a>TransatoAlReplicador

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|BatchAckAckNownowledgementInterval | Tempo em segundos, o padrão é 0.015 | Estático | Especifique a timepan em segundos. Determina o tempo que o replicador espera depois de receber uma operação antes de enviar de volta um aviso. Outras operações recebidas durante este período de tempo terão os seus agradecimentos enviados de volta numa única mensagem-> reduzindo o tráfego de rede, mas potencialmente reduzindo a produção do replicador. |
|MaxCopyQueueSize |Uint, o padrão é 16384 | Estático |Este é o valor máximo que define o tamanho inicial para a fila que mantém as operações de replicação. Note que deve ser um poder de 2. Se durante o tempo de funcionamento a fila crescer para esta operação de tamanho será acelerada entre os replicadores primários e secundários. |
|MaxPrimaryReplicationQueueMemorySize |Uint, o padrão é 0 | Estático |Este é o valor máximo da fila de replicação primária em bytes. |
|MaxPrimaryReplicationQueueSize |Uint, o padrão é 8192 | Estático |Este é o número máximo de operações que podem existir na fila de replicação primária. Note que deve ser um poder de 2. |
|MaxReplicationMessageSize |Uint, o padrão é 52428800 | Estático | Tamanho máximo da mensagem das operações de replicação. O padrão é de 50MB. |
|MaxSecondaryReplicationQueueMemorySize |Uint, o padrão é 0 | Estático |Este é o valor máximo da fila de replicação secundária em bytes. |
|MaxSecondaryReplicationSize |Uint, o padrão é 16384 | Estático |Este é o número máximo de operações que podem existir na fila de replicação secundária. Note que deve ser um poder de 2. |
|ReplicatorAddress |cadeia, padrão é "localhost:0" | Estático | O ponto final sob a forma de uma cadeia -'IP:Port' que é utilizada pelo Replicador de Tecidos windows para estabelecer ligações com outras réplicas para enviar/receber operações. |

## <a name="transport"></a>Transporte
| **Parâmetro** | **Valores Permitidos** |**Política de upgrade** |**Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|ConnectionOpenTimeout|TimeSpan, o padrão é Comum::TimeSpan::FromSeconds(60)|Estático|Especifique a timepan em segundos. Tempo de saída para a configuração da ligação tanto no lado de entrada como aceitação (incluindo negociação de segurança em modo seguro) |
|FrameHeaderErrorCheckingEnabled|bool, o padrão é VERDADEIRO|Estático|Definição predefinição para verificação de erros no cabeçalho do quadro no modo não seguro; a definição de componentes substitui isto. |
|MessageErrorCheckingEnabled|bool,padrão é FALSO|Estático|Definição predefinição para verificação de erros no cabeçalho de mensagem e no corpo no modo não seguro; a definição de componentes substitui isto. |
|ResolveOption|cadeia, o padrão é "não especificado"|Estático|Determina como o FQDN é resolvido.  Os valores válidos são "não especificados/ipv4/ipv6". |
|SendTimeout|TimeSpan, o padrão é Comum::TimeSpan::FromSeconds(300)|Dinâmica|Especifique a timepan em segundos. Envie um tempo para detetar a ligação presa. Os relatórios de falhas da TCP não são fiáveis em algum ambiente. Isto pode ter de ser ajustado de acordo com a largura de banda de rede disponível e o tamanho dos dados de saída \* (MaxMessageSize \/ \* SendQueueSizeLimit). |

## <a name="upgradeorchestrationservice"></a>UpgradeOrchestrationService

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|AutoupgradeEnabled | Bool, o padrão é verdadeiro |Estático| Sondagem automática e ação de upgrade com base num ficheiro de estado-golo. |
|AutoupgradeInstallEnabled|Bool, o padrão é FALSO|Estático|Sondagem automática, provisionamento e instalação de ação de atualização de códigos com base num ficheiro de estado-golo.|
|GoalStateExpirationReminderInDays|int, o padrão é 30|Estático|Define o número de dias restantes após o qual deve ser mostrado o lembrete do estado do objetivo.|
|MinReplicaSetsize |Int, o padrão é 0 |Estático |O MinReplicaSetSize para UpgradeOrchestrationService.|
|ColocaçãoConstratas | cadeia, padrão é "" |Estático| Os Estados-Maiores de Colocação para UpgradeOrchestrationService. |
|QurumLossWaitDuration | Tempo em segundos, o padrão é MaxValue |Estático| Especifique a timepan em segundos. O QuorumLossWaitDuration for UpgradeOrchestrationService. |
|ReplicaRestartWaitDuration | Tempo em segundos, o padrão é de 60 minutos|Estático| Especifique a timepan em segundos. O ReplicaRestartWaitDuration for UpgradeOrchestrationService. |
|StandByReplicaKeepDuration | Tempo em segundos, o padrão é 60*24*7 minutos |Estático| Especifique a timepan em segundos. O StandByReplicaKeepDuration for UpgradeOrchestrationService. |
|TargetReplicaSetsize |Int, o padrão é 0 |Estático |O TargetReplicaSetSize para UpgradeOrchestrationService. |
|UpgradeApprovalRequired | Bool, o padrão é falso. | Estático|A definição para fazer a atualização de código requer a aprovação do administrador antes de prosseguir. |

## <a name="upgradeservice"></a>UpgradeService

| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|BaseUrl | cadeia, padrão é "" |Estático|BaseUrl para UpgradeService. |
|ClusterId | cadeia, padrão é "" |Estático|ClusterId para UpgradeService. |
|CoordenadorType | cadeia, padrão é "WUTest"|Não é permitido|O CoordenadorType para UpgradeService. |
|MinReplicaSetsize | Int, o padrão é 2 |Não é permitido| O MinReplicaSetSize para UpgradeService. |
|ApenasBaseUprado | Bool, o padrão é falso. |Dinâmica|ApenasBaseUpgrade para UpgradeService. |
|ColocaçãoConstratas |cadeia, padrão é "" |Não é permitido|Os PlacementConstraints para o serviço de upgrade. |
|PollIntervalInSeconds|Timepan, o padrão é Comum::TimeSpan::FromSeconds(60) |Dinâmica|Especifique a timepan em segundos. O intervalo entre a sondagem UpgradeService para operações de gestão ARM. |
|TargetReplicaSetsize | Int, o padrão é 3 |Não é permitido| O TargetReplicaSetSize para UpgradeService. |
|TestCabFolder | cadeia, padrão é "" |Estático| TestCabFolder para UpgradeService. |
|X509FindType | cadeia, padrão é ""|Dinâmica| X509FindType para UpgradeService. |
|X509FindValue | cadeia, padrão é "" |Dinâmica| X509FindValue para UpgradeService. |
|X509SecondaryFindValue | cadeia, padrão é "" |Dinâmica| X509SecondaryFindValue para UpgradeService. |
|X509StoreLocation | cadeia, padrão é "" |Dinâmica| X509StoreLocation para UpgradeService. |
|X509StoreName | cadeia, padrão é "My"|Dinâmica|X509StoreName para UpgradeService. |

## <a name="userservicemetriccapacities"></a>CapacidadesMétricas do Serviço do Utilizador
| **Parâmetro** | **Valores Permitidos** | **Política de Upgrade** | **Orientação ou Breve Descrição** |
| --- | --- | --- | --- |
|Grupo de Propriedades| UserServiceMetricCapacitiesMap, predefinição é nenhum | Estático | Uma recolha de limites de governação de recursos de serviços de utilizador precisa de ser estática, uma vez que afeta a lógica de AutoDetecção |

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações, consulte [atualizar a configuração de um cluster Azure](service-fabric-cluster-config-upgrade-azure.md) e [atualizar a configuração de um cluster autónomo](service-fabric-cluster-config-upgrade-windows-server.md).