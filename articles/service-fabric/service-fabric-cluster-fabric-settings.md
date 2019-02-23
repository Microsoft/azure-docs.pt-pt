---
title: Alterar as definições de cluster do Azure Service Fabric | Documentos da Microsoft
description: Este artigo descreve as definições de recursos de infraestrutura e as políticas de atualização de recursos de infraestrutura que pode personalizar.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: 7ced36bf-bd3f-474f-a03a-6ebdbc9677e2
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/11/2018
ms.author: aljo
ms.openlocfilehash: c8cfa0174d3e3300bdc3cfbc68ca416d9b736300
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56674910"
---
# <a name="customize-service-fabric-cluster-settings"></a>Personalize as configurações de cluster do Service Fabric
Este artigo descreve as várias configurações de recursos de infraestrutura para o seu cluster do Service Fabric que pode personalizar. Para clusters alojados no Azure, pode personalizar as definições através da [portal do Azure](https://portal.azure.com) ou utilizando um modelo Azure Resource Manager. Para obter mais informações, consulte [atualizar a configuração de um cluster do Azure](service-fabric-cluster-config-upgrade-azure.md). Para clusters autónomos, personalizar as definições ao atualizar o *ClusterConfig.json* de atualização de ficheiro e efetuar uma configuração no seu cluster. Para obter mais informações, consulte [atualizar a configuração de um cluster autónomo](service-fabric-cluster-config-upgrade-windows-server.md).

Existem três políticas de atualização diferentes:

- **Dinâmica** – alterações a uma configuração dinâmica não causam os reinícios do processo de processos do Service Fabric ou seus processos de anfitrião do serviço. 
- **Estático** – alterações a uma configuração estática fará com que o nó do Service Fabric reiniciar para consumir a alteração. Serviços em nós serão reiniciados.
- **NotAllowed** – estas definições não podem ser modificadas. Alterar estas definições requer que o cluster ser destruído e criado um novo cluster. 

Segue-se uma lista dos recursos de infraestrutura, as definições que pode personalizar, organizados pela secção.

## <a name="applicationgatewayhttp"></a>ApplicationGateway/Http
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou descrição breve** |
| --- | --- | --- | --- |
|ApplicationCertificateValidationPolicy|cadeia de caracteres, predefinido é "None"|Estático| Isso não valida o certificado de servidor; concluída com êxito o pedido. Consulte a configuração ServiceCertificateThumbprints para obter a lista separada por vírgulas de thumbprints de certificados remotos que pode confiar o proxy inverso. Consulte a configuração ServiceCommonNameAndIssuer para o thumbprint de nome e o emissor de assunto dos certificados remotos que pode confiar o proxy inverso. Para obter mais informações, consulte [inverter a ligação segura de proxy](service-fabric-reverseproxy-configure-secure-communication.md#secure-connection-establishment-between-the-reverse-proxy-and-services). |
|BodyChunkSize |Uint, a predefinição é 16384 |Dinâmica| Fornece o tamanho para o segmento em bytes, usado para ler o corpo. |
|CrlCheckingFlag|uint, a predefinição é 0x40000000 |Dinâmica| Sinalizadores de validação da cadeia de certificado de aplicação/serviço; Por exemplo, 0x10000000 de verificação de CRL CERT_CHAIN_REVOCATION_CHECK_END_CERT 0x20000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN 0x40000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT 0x80000000 CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY definição como 0 dwFlags de CertGetCertificateChain é documentada desativa CRL verificação lista, completa, de valores suportados: http://msdn.microsoft.com/library/windows/desktop/aa376078(v=vs.85).aspx  |
|DefaultHttpRequestTimeout |Tempo em segundos. a predefinição é 120 |Dinâmica|Especifique o período de tempo em segundos.  Fornece o tempo de limite de solicitação padrão para os pedidos de http a ser processados no gateway de aplicação de http. |
|ForwardClientCertificate|bool, a predefinição é falso|Dinâmica|Quando definido como false, inverta proxy não irá pedir para o certificado de cliente. Quando definido como true, inversa proxy irá pedir para o certificado de cliente durante o handshake SSL e reencaminhar o codificada em base64 a cadeia de formato PEM para o serviço num cabeçalho X-Client-Certificate.The serviço com o nome pode falhar o pedido com o código de estado apropriado Depois inspecionar os dados do certificado. Se isso é verdadeiro e o cliente não apresenta um certificado, o proxy inverso reencaminhar um cabeçalho vazio e permitir que o serviço de administrar o caso. Proxy inverso irão funcionar como uma camada transparente. Para obter mais informações, consulte [configurar a autenticação de certificado de cliente](service-fabric-reverseproxy-configure-secure-communication.md#setting-up-client-certificate-authentication-through-the-reverse-proxy). |
|GatewayAuthCredentialType |cadeia de caracteres, predefinido é "None" |Estático| Indica o tipo de credenciais de segurança para utilizar os http aplicação gateway ponto final valores válidos são "nenhum / X 509. |
|GatewayX509CertificateFindType |cadeia de caracteres, predefinido é "FindByThumbprint" |Dinâmica| Indica como procurar o certificado no arquivo especificado pelo valor GatewayX509CertificateStoreName suportados: FindByThumbprint; FindBySubjectName. |
|GatewayX509CertificateFindValue | cadeia de caracteres, a predefinição é "" |Dinâmica| Valor de filtro de pesquisa utilizado para localizar o certificado de gateway de aplicação de http. Este certificado é configurado no ponto final https e também pode ser utilizado para verificar a identidade da aplicação, se necessário, os serviços. FindValue é verificado primeiro; e se não existir; FindValueSecondary é pesquisado. |
|GatewayX509CertificateFindValueSecondary | cadeia de caracteres, a predefinição é "" |Dinâmica|Valor de filtro de pesquisa utilizado para localizar o certificado de gateway de aplicação de http. Este certificado é configurado no ponto final https e também pode ser utilizado para verificar a identidade da aplicação, se necessário, os serviços. FindValue é verificado primeiro; e se não existir; FindValueSecondary é pesquisado.|
|GatewayX509CertificateStoreName |cadeia de caracteres, a predefinição é "Meu" |Dinâmica| Nome do arquivo de certificados X.509, que contém o certificado para o gateway de aplicação de http. |
|HttpRequestConnectTimeout|Período de tempo, a predefinição é Common::TimeSpan::FromSeconds(5)|Dinâmica|Especifique o período de tempo em segundos.  Fornece o tempo limite de ligar para os pedidos de http enviados a partir do gateway de aplicação de http.  |
|IgnoreCrlOfflineError|bool, a predefinição é TRUE|Dinâmica|Se ignorar erro offline de CRL para a verificação de certificado de aplicação/serviço. |
|IsEnabled |Bool, a predefinição é falso |Estático| Ativa/desativa o HttpApplicationGateway. HttpApplicationGateway está desativada por predefinição e esta configuração precisa ser definida para ativá-la. |
|NumberOfParallelOperations | Uint, a predefinição é 5000 |Estático|Número de leituras por oposição a publicar na fila de servidor http. Esse item controla o número de pedidos simultâneos que podem ser concluídas ao HttpGateway. |
|RemoveServiceResponseHeaders|cadeia de caracteres, predefinido é "data; Servidor"|Estático|Dois pontos de volumes / separados por vírgulas lista de cabeçalhos de resposta será removido da resposta do serviço; antes do reencaminhamento-lo ao cliente. Se isto estiver definido como uma cadeia vazia; passar todos os cabeçalhos devolvidos pelo serviço como-é. ou seja Não substituir a data e o servidor |
|ResolveServiceBackoffInterval |Tempo em segundos, a predefinição é 5 |Dinâmica|Especifique o período de tempo em segundos.  Fornece a resolver o intervalo de término do padrão antes de repetir uma falha na operação de serviço. |
|SecureOnlyMode|bool, a predefinição é falso|Dinâmica| SecureOnlyMode: verdadeiro: Proxy inverso apenas irá reencaminhar para serviços que publicar pontos de extremidade seguros. FALSE: Proxy inverso pode encaminhar pedidos para pontos finais secure/não segura. Para obter mais informações, consulte [inverter a lógica de seleção de ponto de extremidade do proxy](service-fabric-reverseproxy-configure-secure-communication.md#endpoint-selection-logic-when-services-expose-secure-as-well-as-unsecured-endpoints).  |
|ServiceCertificateThumbprints|cadeia de caracteres, a predefinição é ""|Dinâmica|Lista separada por vírgulas de thumbprints de certificados remotos que pode confiar o proxy inverso. Para obter mais informações, consulte [inverter a ligação segura de proxy](service-fabric-reverseproxy-configure-secure-communication.md#secure-connection-establishment-between-the-reverse-proxy-and-services). |

## <a name="applicationgatewayhttpservicecommonnameandissuer"></a>ApplicationGateway/Http/ServiceCommonNameAndIssuer
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou descrição breve** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, a predefinição é nenhum|Dinâmica| Sujeita thumbprint de nome e o emissor dos certificados remotos que pode confiar o proxy inverso. Para obter mais informações, consulte [inverter a ligação segura de proxy](service-fabric-reverseproxy-configure-secure-communication.md#secure-connection-establishment-between-the-reverse-proxy-and-services). |

## <a name="backuprestoreservice"></a>BackupRestoreService
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou descrição breve** |
| --- | --- | --- | --- |
|MinReplicaSetSize|int, a predefinição é 0|Estático|MinReplicaSetSize para BackupRestoreService |
|PlacementConstraints|cadeia de caracteres, a predefinição é ""|Estático|  PlacementConstraints para o serviço de BackupRestore |
|SecretEncryptionCertThumbprint|cadeia de caracteres, a predefinição é ""|Dinâmica|Thumbprint do certificado X509 de criptografia Secret |
|SecretEncryptionCertX509StoreName|cadeia de caracteres, a predefinição é "Meu"|   Dinâmica|    Isto indica que o certificado a utilizar para encriptação e desencriptação de arquivo de certificados de nome de X.509 de credenciais é utilizado para encriptar a desencriptar credenciais de armazenamento utilizadas pelo serviço de restaurar a cópia de segurança |
|TargetReplicaSetSize|int, a predefinição é 0|Estático| TargetReplicaSetSize para BackupRestoreService |

## <a name="clustermanager"></a>ClusterManager
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou descrição breve** |
| --- | --- | --- | --- |
|EnableDefaultServicesUpgrade | Bool, a predefinição é falso |Dinâmica|Ative os serviços de predefinição atualizar durante a atualização da aplicação. As descrições de serviço predefinido serão substituídas após a atualização. |
|FabricUpgradeHealthCheckInterval |Tempo em segundos, a predefinição é 60 |Dinâmica|A frequência de estado de funcionamento verificar durante uma atualização do Fabric monitorizada |
|FabricUpgradeStatusPollInterval |Tempo em segundos, a predefinição é 60 |Dinâmica|A freqüência de sondagem de status de atualização de recursos de infraestrutura. Este valor determina a taxa de atualização para qualquer chamada GetFabricUpgradeProgress |
|ImageBuilderTimeoutBuffer |Tempo em segundos, a predefinição é 3 |Dinâmica|Especifique o período de tempo em segundos. A quantidade de tempo para permitir a existência de erros de tempo limite específico de construtor de imagens devolver ao cliente. Se esse buffer for demasiado pequeno; em seguida, o cliente exceder o tempo limite antes do servidor e obtém um erro de tempo limite genérico. |
|InfrastructureTaskHealthCheckRetryTimeout | Tempo em segundos, a predefinição é 60 |Dinâmica|Especifique o período de tempo em segundos. A quantidade de tempo para repetir a operação falha verificações de estado de funcionamento durante o processamento pós-cópia de uma tarefa de infraestrutura. Observar uma verificação de estado de funcionamento com êxito irá repor este temporizador. |
|InfrastructureTaskHealthCheckStableDuration | Tempo em segundos, o padrão é 0|Dinâmica| Especifique o período de tempo em segundos. A quantidade de tempo para observar as verificações de estado de funcionamento consecutivas com êxito antes de pós-processamento de uma tarefa de infraestrutura é concluída com êxito. Observar uma verificação de estado de funcionamento com falha irá repor este temporizador. |
|InfrastructureTaskHealthCheckWaitDuration |Tempo em segundos, o padrão é 0|Dinâmica| Especifique o período de tempo em segundos. A quantidade de tempo de espera antes de iniciar as verificações de estado de funcionamento após pós-processamento de uma tarefa de infraestrutura. |
|InfrastructureTaskProcessingInterval | Tempo em segundos, o padrão é 10 |Dinâmica|Especifique o período de tempo em segundos. O intervalo de processamento utilizado pela máquina de estado de processamento de tarefas de infraestrutura. |
|MaxCommunicationTimeout |Tempo em segundos, a predefinição é de 600 |Dinâmica|Especifique o período de tempo em segundos. O tempo limite máximo para as comunicações internas entre ClusterManager e outro sistema de serviços (ou seja, Serviço de nomenclatura; Gestor de ativação pós-falha e etc.). Este tempo limite deve ser menor do que global MaxOperationTimeout (como podem haver várias comunicações entre os componentes de sistema para cada operação de cliente). |
|MaxDataMigrationTimeout |Tempo em segundos, a predefinição é de 600 |Dinâmica|Especifique o período de tempo em segundos. O tempo limite máximo para operações de recuperação para migração de dados após a ocorrência de uma atualização do Fabric. |
|MaxOperationRetryDelay |Tempo em segundos, a predefinição é 5|Dinâmica| Especifique o período de tempo em segundos. O atraso máximo de tentativas internas quando são encontradas falhas. |
|MaxOperationTimeout |Tempo em segundos, a predefinição é MaxValue |Dinâmica| Especifique o período de tempo em segundos. O máximo tempo limite global para operações em ClusterManager de processamento internamente. |
|MaxTimeoutRetryBuffer | Tempo em segundos, a predefinição é de 600 |Dinâmica|Especifique o período de tempo em segundos. É o tempo limite da operação máximo quando internamente repetir a operação devido a tempos limite <Original Time out>  +  <MaxTimeoutRetryBuffer>. Tempo limite adicional é adicionado em incrementos de MinOperationTimeout. |
|MinOperationTimeout | Tempo em segundos, a predefinição é 60 |Dinâmica|Especifique o período de tempo em segundos. O mínimo tempo limite global para operações em ClusterManager de processamento internamente. |
|MinReplicaSetSize |Int, a predefinição é 3 |Não Permitido|O MinReplicaSetSize para ClusterManager. |
|PlacementConstraints | cadeia de caracteres, a predefinição é "" |Não Permitido|O PlacementConstraints para ClusterManager. |
|QuorumLossWaitDuration |Tempo em segundos, a predefinição é MaxValue |Não Permitido| Especifique o período de tempo em segundos. O QuorumLossWaitDuration para ClusterManager. |
|ReplicaRestartWaitDuration |Tempo em segundos, a predefinição é (60.0 * 30)|Não Permitido|Especifique o período de tempo em segundos. O ReplicaRestartWaitDuration para ClusterManager. |
|ReplicaSetCheckTimeoutRollbackOverride |Tempo em segundos, a predefinição é 1200 |Dinâmica| Especifique o período de tempo em segundos. Se ReplicaSetCheckTimeout estiver definido como o valor máximo de DWORD; em seguida, é substituído pelo valor desta configuração para fins de reversão. O valor utilizado para roll-forward nunca é substituído. |
|SkipRollbackUpdateDefaultService | Bool, a predefinição é falso |Dinâmica|O CM irá ignorar a reverter serviços de predefinição atualizados durante a reversão de atualização de aplicação. |
|StandByReplicaKeepDuration | Tempo em segundos, a predefinição é (3600.0 * 2)|Não Permitido|Especifique o período de tempo em segundos. O StandByReplicaKeepDuration para ClusterManager. |
|TargetReplicaSetSize |Int, o padrão é 7 |Não Permitido|O TargetReplicaSetSize para ClusterManager. |
|UpgradeHealthCheckInterval |Tempo em segundos, a predefinição é 60 |Dinâmica|A frequência de estado de funcionamento verifica durante uma atualização da aplicação monitorizados |
|UpgradeStatusPollInterval |Tempo em segundos, a predefinição é 60 |Dinâmica|A freqüência de sondagem de status de atualização de aplicação. Este valor determina a taxa de atualização para qualquer chamada GetApplicationUpgradeProgress |

## <a name="common"></a>Common
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou descrição breve** |
| --- | --- | --- | --- |
|PerfMonitorInterval |Tempo em segundos, a predefinição é 1 |Dinâmica|Especifique o período de tempo em segundos. Intervalo de monitorização de desempenho. A configuração para valor negativo ou 0 desativa a monitorização. |

## <a name="defragmentationemptynodedistributionpolicy"></a>DefragmentationEmptyNodeDistributionPolicy
| **Parâmetro** | **Valores permitidos** |**Política de atualização**| **Documentação de orientação ou descrição breve** |
| --- | --- | --- | --- |
|PropertyGroup|KeyIntegerValueMap, a predefinição é nenhum|Dinâmica|Especifica que a desfragmentação de política segue quando a esvaziar nós. Para uma determinada métrica 0 indica que deve tentar SF desfragmentar nós uniformemente em UDs e FDs; 1 indica apenas que os nós têm desfragmentados |

## <a name="defragmentationmetrics"></a>DefragmentationMetrics
| **Parâmetro** | **Valores permitidos** |**Política de atualização**| **Documentação de orientação ou descrição breve** |
| --- | --- | --- | --- |
|PropertyGroup|KeyBoolValueMap, a predefinição é nenhum|Dinâmica|Determina o conjunto de métricas que devem ser utilizados para a desfragmentação e não para balanceamento de carga. |

## <a name="defragmentationmetricspercentornumberofemptynodestriggeringthreshold"></a>DefragmentationMetricsPercentOrNumberOfEmptyNodesTriggeringThreshold
| **Parâmetro** | **Valores permitidos** |**Política de atualização**| **Documentação de orientação ou descrição breve** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, a predefinição é nenhum|Dinâmica|Determina o número de nós livres que são necessários a serem considerados cluster desfragmentado, especificando ambos % no intervalo [0.0-1.0) ou número de nós vazios como número de > = 1,0 |

## <a name="diagnostics"></a>Diagnóstico
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou descrição breve** |
| --- | --- | --- | --- |
|AppDiagnosticStoreAccessRequiresImpersonation |Bool, a predefinição é verdadeiro | Dinâmica |Se pretende ou não representação é necessária para aceder ao diagnóstico armazena em nome do aplicativo. |
|AppEtwTraceDeletionAgeInDays |Int, a predefinição é 3 | Dinâmica |Número de dias após o qual podemos eliminar ficheiros ETL antigos, que contém os rastreios ETW de aplicações. |
|ApplicationLogsFormatVersion |int, a predefinição é 0 | Dinâmica |Formato de registos de versão para a aplicação. Os valores suportados são 0 e 1. Versão 1 inclui mais campos do registo de eventos do ETW que versão 0. |
|ClusterId |String | Dinâmica |O id exclusivo do cluster. Isso é gerado quando o cluster é criado. |
|ConsumerInstances |String | Dinâmica |A lista de instâncias de consumidor do DCA. |
|DiskFullSafetySpaceInMB |Int, o padrão é 1024 | Dinâmica |Espaço em disco restante em MB para proteger contra o uso por DCA. |
|EnableCircularTraceSession |Bool, a predefinição é falso | Estático |Sinalizador indica se as sessões de rastreamento circular devem ser utilizados. |
|Ativar telemetria |Bool, a predefinição é verdadeiro | Dinâmica |Isto vai ativar ou desativar a telemetria. |
|MaxDiskQuotaInMB |Int, a predefinição é 65536 | Dinâmica |Quota de disco nos ficheiros de registo de MB para o Windows Fabric. |
|ProducerInstances |String | Dinâmica |A lista de instâncias de produtor DCA. |

## <a name="dnsservice"></a>DnsService
| **Parâmetro** | **Valores permitidos** |**Política de atualização**| **Documentação de orientação ou descrição breve** |
| --- | --- | --- | --- |
|EnablePartitionedQuery|bool, a predefinição é falso|Estático|O sinalizador para ativar o suporte para consultas DNS para serviços particionadas. A funcionalidade está desativada por predefinição. Para obter mais informações, consulte [serviço DNS do Service Fabric.](service-fabric-dnsservice.md)|
|InstanceCount|int, a predefinição é de -1|Estático|valor predefinido é -1, que significa que DnsService está em execução em cada nó. OneBox precisa para ser definido como 1, uma vez que DnsService utiliza a porta conhecida 53, para que ele não pode ter várias instâncias no mesmo computador.|
|IsEnabled|bool, a predefinição é falso|Estático|Ativa/desativa DnsService. DnsService está desativada por predefinição e esta configuração precisa ser definida para ativá-la. |
|PartitionPrefix|cadeia de caracteres, a predefinição é ""|Estático|Controla o valor de cadeia de caracteres de prefixo de partição nas consultas DNS para serviços particionadas. O valor: <ul><li>Deve estar em conformidade com RFC como será a parte de uma consulta DNS.</li><li>Não deve conter um ponto ".", como o ponto interfere com o comportamento de sufixo DNS.</li><li>Não deve ter mais de 5 carateres.</li><li>Não pode ser uma cadeia vazia.</li><li>Se a definição de PartitionPrefix é substituída, então PartitionSuffix tem de ser substituído e vice-versa.</li></ul>Para obter mais informações, consulte [serviço de DNS do Service Fabric.](service-fabric-dnsservice.md).|
|PartitionSuffix|cadeia de caracteres, a predefinição é ""|Estático|Controla o valor de cadeia de caracteres de sufixo de partição nas consultas DNS para serviços particionadas. O valor: <ul><li>Deve estar em conformidade com RFC como será a parte de uma consulta DNS.</li><li>Não deve conter um ponto ".", como o ponto interfere com o comportamento de sufixo DNS.</li><li>Não deve ter mais de 5 carateres.</li><li>Se a definição de PartitionPrefix é substituída, então PartitionSuffix tem de ser substituído e vice-versa.</li></ul>Para obter mais informações, consulte [serviço de DNS do Service Fabric.](service-fabric-dnsservice.md). |

## <a name="eventstore"></a>EventStore
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou descrição breve** |
| --- | --- | --- | --- |
|MinReplicaSetSize|int, a predefinição é 0|Estático|MinReplicaSetSize para o serviço de EventStore |
|PlacementConstraints|cadeia de caracteres, a predefinição é ""|Estático|  PlacementConstraints para o serviço de EventStore |
|TargetReplicaSetSize|int, a predefinição é 0|Estático| TargetReplicaSetSize para o serviço de EventStore |

## <a name="fabricclient"></a>FabricClient
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou descrição breve** |
| --- | --- | --- | --- |
|ConnectionInitializationTimeout |Tempo em segundos, a predefinição é 2 |Dinâmica|Especifique o período de tempo em segundos. Intervalo de tempo limite de ligação para cada cliente de tempo tenta abrir uma ligação para o gateway.|
|HealthOperationTimeout |Tempo em segundos, a predefinição é 120 |Dinâmica|Especifique o período de tempo em segundos. O tempo limite de uma mensagem de relatório enviado para o Gestor de estado de funcionamento. |
|HealthReportRetrySendInterval |Tempo em segundos, a predefinição é 30 |Dinâmica|Especifique o período de tempo em segundos. O intervalo no qual reporting componente envia novamente relatórios de estado de funcionamento acumulado ao Gestor de estado de funcionamento. |
|HealthReportSendInterval |Tempo em segundos, a predefinição é 30 |Dinâmica|Especifique o período de tempo em segundos. O intervalo no qual reporting componente envia relatórios de estado de funcionamento acumulado ao Gestor de estado de funcionamento. |
|KeepAliveIntervalInSeconds |Int, a predefinição é 20 |Estático|O intervalo a que o transporte do FabricClient envia mensagens keep-alive para o gateway. Para 0; keepAlive está desativada. Tem de ser um valor positivo. |
|MaxFileSenderThreads |Uint, a predefinição é 10 |Estático|O número máximo de ficheiros que são transferidos em paralelo. |
|NodeAddresses |cadeia de caracteres, a predefinição é "" |Estático|Uma coleção de endereços (cadeias de ligação) em nós diferentes que podem ser utilizadas para comunicar com o serviço de nomenclatura. Inicialmente o cliente liga-se selecionar um dos endereços aleatoriamente. Se for fornecida mais do que uma cadeia de ligação e uma ligação falha devido a um erro de tempo limite; ou de comunicação os comutadores de cliente para utilizar o endereço do próximo sequencialmente. Ver o endereço do serviço de nomenclatura secção para obter detalhes sobre a semântica de tentativas de repetição. |
|PartitionLocationCacheLimit |Int, a predefinição é 100000 |Estático|Número de partições colocadas em cache para a resolução de serviço (definida como 0 para nenhum limite). |
|RetryBackoffInterval |Tempo em segundos, a predefinição é 3 |Dinâmica|Especifique o período de tempo em segundos. O intervalo de término antes de repetir a operação. |
|ServiceChangePollInterval |Tempo em segundos, a predefinição é 120 |Dinâmica|Especifique o período de tempo em segundos. Altera o intervalo entre consultas consecutivas para o serviço do cliente para o gateway para retornos de chamada de notificações de alteração de serviço registado. |

## <a name="fabrichost"></a>FabricHost
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou descrição breve** |
| --- | --- | --- | --- |
|ActivationMaxFailureCount |Int, a predefinição é 10 |Dinâmica|Esta é a contagem máxima para o qual sistema voltará a tentar ativação com falha antes de desistir. |
|ActivationMaxRetryInterval |Tempo em segundos, a predefinição é de 300 |Dinâmica|Especifique o período de tempo em segundos. Intervalo de repetição máximo para a ativação. Em cada caso de falha contínuo, o intervalo entre tentativas é calculado como Min (ActivationMaxRetryInterval; Número de falhas contínua * ActivationRetryBackoffInterval). |
|ActivationRetryBackoffInterval |Tempo em segundos, a predefinição é 5 |Dinâmica|Especifique o período de tempo em segundos. Intervalo de término em cada caso de falha de ativação; em cada caso de falha de ativação contínua, que o sistema voltará a tentar a ativação de até o MaxActivationFailureCount. O intervalo entre tentativas em cada tente é um produto de falha de ativação contínua e o intervalo de término ativação. |
|EnableRestartManagement |Bool, a predefinição é falso |Dinâmica|Isso é permitir o reinício do servidor. |
|EnableServiceFabricAutomaticUpdates |Bool, a predefinição é falso |Dinâmica|Isso é para ativar a atualização automática de recursos de infraestrutura via Windows Update. |
|EnableServiceFabricBaseUpgrade |Bool, a predefinição é falso |Dinâmica|Isso é para ativar a atualização de base para o servidor. |
|StartTimeout |Tempo em segundos, a predefinição é de 300 |Dinâmica|Especifique o período de tempo em segundos. Tempo limite para fabricactivationmanager de arranque. |
|StopTimeout |Tempo em segundos, a predefinição é de 300 |Dinâmica|Especifique o período de tempo em segundos. O tempo limite para ativação do serviço alojado; a desativação e a atualização. |

## <a name="fabricnode"></a>FabricNode
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou descrição breve** |
| --- | --- | --- | --- |
|ClientAuthX509FindType |cadeia de caracteres, predefinido é "FindByThumbprint" |Dinâmica|Indica como procurar o certificado no arquivo especificado pelo valor ClientAuthX509StoreName suportados: FindByThumbprint; FindBySubjectName. |
|ClientAuthX509FindValue |cadeia de caracteres, a predefinição é "" | Dinâmica|Valor de filtro de pesquisa utilizado para localizar o certificado para a função de administrador predefinida FabricClient. |
|ClientAuthX509FindValueSecondary |cadeia de caracteres, a predefinição é "" |Dinâmica|Valor de filtro de pesquisa utilizado para localizar o certificado para a função de administrador predefinida FabricClient. |
|ClientAuthX509StoreName |cadeia de caracteres, a predefinição é "Meu" |Dinâmica|Nome do arquivo de certificados X.509 que contém o certificado para a função de administrador predefinida FabricClient. |
|ClusterX509FindType |cadeia de caracteres, predefinido é "FindByThumbprint" |Dinâmica|Indica como procurar o certificado de cluster no arquivo especificado por valores ClusterX509StoreName suportados: "FindByThumbprint"; "FindBySubjectName" com "FindBySubjectName"; Quando existem várias correspondências; a expiração furthest é usado. |
|ClusterX509FindValue |cadeia de caracteres, a predefinição é "" |Dinâmica|Valor de filtro de pesquisa utilizado para localizar o certificado de cluster. |
|ClusterX509FindValueSecondary |cadeia de caracteres, a predefinição é "" |Dinâmica|Valor de filtro de pesquisa utilizado para localizar o certificado de cluster. |
|ClusterX509StoreName |cadeia de caracteres, a predefinição é "Meu" |Dinâmica|Nome do arquivo de certificados X.509, que contém o certificado de cluster para proteger a comunicação de dentro do cluster. |
|EndApplicationPortRange |int, a predefinição é 0 |Estático|Fim (não inclusivo) as portas de aplicação gerida pelo subsistema de alojamento. Necessário se EndpointFilteringEnabled é verdadeiro para alojamento. |
|ServerAuthX509FindType |cadeia de caracteres, predefinido é "FindByThumbprint" |Dinâmica|Indica como procurar o certificado de servidor no arquivo especificado pelo valor ServerAuthX509StoreName suportados: FindByThumbprint; FindBySubjectName. |
|ServerAuthX509FindValue |cadeia de caracteres, a predefinição é "" |Dinâmica|Valor de filtro de pesquisa utilizado para localizar o certificado de servidor. |
|ServerAuthX509FindValueSecondary |cadeia de caracteres, a predefinição é "" |Dinâmica|Valor de filtro de pesquisa utilizado para localizar o certificado de servidor. |
|ServerAuthX509StoreName |cadeia de caracteres, a predefinição é "Meu" |Dinâmica|Nome do arquivo de certificados X.509, que contém o certificado de servidor para o serviço de documento. |
|StartApplicationPortRange |int, a predefinição é 0 |Estático|Início das portas de aplicação gerida pelo subsistema de alojamento. Necessário se EndpointFilteringEnabled é verdadeiro para alojamento. |
|StateTraceInterval |Tempo em segundos, a predefinição é de 300 |Estático|Especifique o período de tempo em segundos. O intervalo para rastrear o estado do nó em cada nó e se nós no FM/FMM. |
|UserRoleClientX509FindType |cadeia de caracteres, predefinido é "FindByThumbprint" |Dinâmica|Indica como procurar o certificado no arquivo especificado pelo valor UserRoleClientX509StoreName suportados: FindByThumbprint; FindBySubjectName. |
|UserRoleClientX509FindValue |cadeia de caracteres, a predefinição é "" |Dinâmica|Valor de filtro de pesquisa utilizado para localizar o certificado para a função de utilizador padrão FabricClient. |
|UserRoleClientX509FindValueSecondary |cadeia de caracteres, a predefinição é "" |Dinâmica|Valor de filtro de pesquisa utilizado para localizar o certificado para a função de utilizador padrão FabricClient. |
|UserRoleClientX509StoreName |cadeia de caracteres, a predefinição é "Meu" |Dinâmica|Nome do arquivo de certificados X.509 que contém o certificado para a função de utilizador padrão FabricClient. |

## <a name="failovermanager"></a>FailoverManager
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou descrição breve** |
| --- | --- | --- | --- |
|BuildReplicaTimeLimit|Período de tempo, a predefinição é Common::TimeSpan::FromSeconds(3600)|Dinâmica|Especifique o período de tempo em segundos. O limite de tempo para a criação de uma réplica com monitoração de estado; após o qual será iniciado um relatório de estado de funcionamento de aviso |
|ClusterPauseThreshold|int, a predefinição é 1|Dinâmica|Se o número de nós no sistema de ir abaixo deste valor, em seguida, o posicionamento; carregar balanceamento; e ativação pós-falha está parado. |
|CreateInstanceTimeLimit|Período de tempo, a predefinição é Common::TimeSpan::FromSeconds(300)|Dinâmica|Especifique o período de tempo em segundos. O limite de tempo para a criação de uma instância sem monitoração de estado; após o qual será iniciado um relatório de estado de funcionamento de aviso |
|ExpectedClusterSize|int, a predefinição é 1|Dinâmica|Quando o cluster é inicialmente foi iniciado; o FM irá esperar, para isso, muitos de nós para comunicar-se a antes de iniciar a colocação de outros serviços; incluindo os serviços do sistema, como a atribuição de nomes. Aumento deste valor aumenta o tempo que demora um cluster para arrancar; mas impede os nós de início de ficar sobrecarregado e também as movimentações adicionais que serão necessárias à medida que mais nós fique online. Este valor em geral, deve ser definido como alguns pequena fração do tamanho do cluster inicial. |
|ExpectedNodeDeactivationDuration|Período de tempo, a predefinição é Common::TimeSpan::FromSeconds(60.0 * 30)|Dinâmica|Especifique o período de tempo em segundos. Esta é a duração esperada para um nó concluir a desativação no. |
|ExpectedNodeFabricUpgradeDuration|Período de tempo, a predefinição é Common::TimeSpan::FromSeconds(60.0 * 30)|Dinâmica|Especifique o período de tempo em segundos. Esta é a duração esperada para um nó a ser atualizado durante a atualização do Windows Fabric. |
|ExpectedReplicaUpgradeDuration|Período de tempo, a predefinição é Common::TimeSpan::FromSeconds(60.0 * 30)|Dinâmica|Especifique o período de tempo em segundos. Esta é a duração esperada para todas as réplicas ser actualizado num nó durante a atualização da aplicação. |
|IsSingletonReplicaMoveAllowedDuringUpgrade|bool, a predefinição é TRUE|Dinâmica|Se definido como true; réplicas com um tamanho de conjunto de réplicas de destino de 1 terão permissão para mover durante a atualização. |
|MinReplicaSetSize|Int, a predefinição é 3|Não Permitido|Este é o tamanho de conjunto mínimo de réplicas para o FM. Se o número de réplicas do Active Directory FM descer abaixo este valor; o FM irão rejeitar as alterações para o cluster até que pelo menos o número mínimo de réplicas é recuperado |
|PlacementConstraints|cadeia de caracteres, a predefinição é ""|Não Permitido|Quaisquer restrições de posicionamento para as réplicas do Gestor de ativação pós-falha |
|PlacementTimeLimit|Período de tempo, a predefinição é Common::TimeSpan::FromSeconds(600)|Dinâmica|Especifique o período de tempo em segundos. O limite de tempo para atingir a contagem de réplicas de destino; após o qual será iniciado um relatório de estado de funcionamento de aviso |
|QuorumLossWaitDuration |Tempo em segundos, a predefinição é MaxValue |Dinâmica|Especifique o período de tempo em segundos. Esta é a duração máxima para o qual podemos permitir uma partição para estar num Estado de perda de quórum. Se a partição está ainda em perda de quórum após esta duração; a partição é recuperada da perda de quórum Considerando as réplicas baixo como perdido. Tenha em atenção que isto pode potencialmente incorrer perda de dados. |
|ReconfigurationTimeLimit|Período de tempo, a predefinição é Common::TimeSpan::FromSeconds(300)|Dinâmica|Especifique o período de tempo em segundos. O limite de tempo para reconfiguração; após o qual será iniciado um relatório de estado de funcionamento de aviso |
|ReplicaRestartWaitDuration|Período de tempo, a predefinição é Common::TimeSpan::FromSeconds(60.0 * 30)|Não Permitido|Especifique o período de tempo em segundos. Este é o ReplicaRestartWaitDuration para o FMService |
|StandByReplicaKeepDuration|Período de tempo, a predefinição é Common::TimeSpan::FromSeconds(3600.0 * 24 * 7)|Não Permitido|Especifique o período de tempo em segundos. Este é o StandByReplicaKeepDuration para o FMService |
|TargetReplicaSetSize|Int, o padrão é 7|Não Permitido|Este é o número de destino das réplicas de FM que irá manter recursos de infraestrutura do Windows. Um número mais alto resulta em maior fiabilidade dos dados FM; com uma compensação de desempenho pequeno. |
|UserMaxStandByReplicaCount |int, a predefinição é 1 |Dinâmica|O número máximo predefinido de réplicas de modo de espera que o sistema mantém-se para os serviços de utilizador. |
|UserReplicaRestartWaitDuration |Tempo em segundos, a predefinição é 60.0 * 30 |Dinâmica|Especifique o período de tempo em segundos. Quando uma réplica persistente fica inativo; Recursos de infraestrutura do Windows é aguarda por esta duração para a réplica seja aberto antes de criar novos réplicas de substituição (o que exigiriam uma cópia do Estado). |
|UserStandByReplicaKeepDuration |Tempo em segundos, o padrão é 3600.0 * 24 * 7 |Dinâmica|Especifique o período de tempo em segundos. Quando uma réplica persistente voltar atrás de um Estado para baixo; Pode já ter foi substituído. Este temporizador determina quanto o FM irá manter a réplica em modo de espera antes de descartá-la. |

## <a name="faultanalysisservice"></a>FaultAnalysisService
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou descrição breve** |
| --- | --- | --- | --- |
|CompletedActionKeepDurationInSeconds | Int, a predefinição é 604800 |Estático| Isto é, aproximadamente quanto para manter as ações que estão num Estado terminal. Isso também depende de segurança StoredActionCleanupIntervalInSeconds; uma vez que o trabalho para a limpeza só é feito nesse intervalo. 604800 é de 7 dias. |
|DataLossCheckPollIntervalInSeconds|int, a predefinição é 5|Estático|Este é o tempo entre as verificações do que sistema executa enquanto aguarda a perda de dados ocorrer. O número de vezes que o número de perda de dados será verificado por iteração interna é DataLossCheckWaitDurationInSeconds/este. |
|DataLossCheckWaitDurationInSeconds|int, a predefinição é 25|Estático|A quantidade total de tempo; em segundos; que o sistema esperará para perda de dados ocorrer. Isto é utilizado internamente quando a api de StartPartitionDataLossAsync() é chamado. |
|MinReplicaSetSize |int, a predefinição é 0 |Estático|O MinReplicaSetSize para FaultAnalysisService. |
|PlacementConstraints | cadeia de caracteres, a predefinição é ""|Estático| O PlacementConstraints para FaultAnalysisService. |
|QuorumLossWaitDuration | Tempo em segundos, a predefinição é MaxValue |Estático|Especifique o período de tempo em segundos. O QuorumLossWaitDuration para FaultAnalysisService. |
|ReplicaDropWaitDurationInSeconds|int, a predefinição é de 600|Estático|Este parâmetro é utilizado quando a perda de dados é chamada de api. Controla o tempo que o sistema esperará para uma réplica para são removidas depois de remover réplica internamente é invocada no mesmo. |
|ReplicaRestartWaitDuration |Tempo em segundos, a predefinição é 60 minutos|Estático|Especifique o período de tempo em segundos. O ReplicaRestartWaitDuration para FaultAnalysisService. |
|StandByReplicaKeepDuration| Tempo em segundos, a predefinição é (60*24*7) minutos |Estático|Especifique o período de tempo em segundos. O StandByReplicaKeepDuration para FaultAnalysisService. |
|StoredActionCleanupIntervalInSeconds | Int, a predefinição é 3600 |Estático|Esta é a frequência com que o arquivo serão limpos. Apenas as ações num Estado terminal; e que foi concluída, pelo menos, CompletedActionKeepDurationInSeconds há será removido. |
|StoredChaosEventCleanupIntervalInSeconds | Int, a predefinição é 3600 |Estático|Esta é a frequência com que o arquivo será auditado para a limpeza; Se o número de eventos é mais do que 30000; a limpeza será iniciada. |
|TargetReplicaSetSize |int, a predefinição é 0 |Estático|NOT_PLATFORM_UNIX_START TargetReplicaSetSize para FaultAnalysisService. |

## <a name="federation"></a>de Federação
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou descrição breve** |
| --- | --- | --- | --- |
|LeaseDuration |Tempo em segundos, a predefinição é 30 |Dinâmica|Duração que dura de uma concessão entre um nó e de seus vizinhos. |
|LeaseDurationAcrossFaultDomain |Tempo em segundos, a predefinição é 30 |Dinâmica|Duração que dura de uma concessão entre um nó e de seus vizinhos entre domínios de falha. |

## <a name="filestoreservice"></a>FileStoreService
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou descrição breve** |
| --- | --- | --- | --- |
|AcceptChunkUpload|bool, a predefinição é TRUE|Dinâmica|Configuração para determinar se o serviço de armazenamento de ficheiros aceita o carregamento de ficheiros de segmentos com base ou não durante o pacote de aplicação de cópia. |
|AnonymousAccessEnabled | Bool, a predefinição é verdadeiro |Estático|Ativar/desativar o acesso anónimo a partilhas de FileStoreService. |
|CommonName1Ntlmx509CommonName|cadeia de caracteres, a predefinição é ""|Estático| O nome comum do X509 certificado utilizado para gerar HMAC no CommonName1NtlmPasswordSecret ao utilizar a autenticação NTLM |
|CommonName1Ntlmx509StoreLocation|cadeia de caracteres, predefinido é "LocalMachine"|Estático|A localização do arquivo de X509 certificado utilizado para gerar HMAC no CommonName1NtlmPasswordSecret ao utilizar a autenticação NTLM |
|CommonName1Ntlmx509StoreName|cadeia de caracteres, predefinido é "MY"| Estático|O nome de arquivo de X509 certificado utilizado para gerar HMAC no CommonName1NtlmPasswordSecret ao utilizar a autenticação NTLM |
|CommonName2Ntlmx509CommonName|cadeia de caracteres, a predefinição é ""|Estático|O nome comum do X509 certificado utilizado para gerar HMAC no CommonName2NtlmPasswordSecret ao utilizar a autenticação NTLM |
|CommonName2Ntlmx509StoreLocation|cadeia de caracteres, predefinido é "LocalMachine"| Estático|A localização do arquivo de X509 certificado utilizado para gerar HMAC no CommonName2NtlmPasswordSecret ao utilizar a autenticação NTLM |
|CommonName2Ntlmx509StoreName|cadeia de caracteres, predefinido é "MY"|Estático| O nome de arquivo de X509 certificado utilizado para gerar HMAC no CommonName2NtlmPasswordSecret ao utilizar a autenticação NTLM |
|CommonNameNtlmPasswordSecret|SecureString, a predefinição é Common::SecureString("")| Estático|O segredo de palavra-passe que utilizado como base para a mesma palavra-passe gerada ao utilizar a autenticação NTLM |
|GenerateV1CommonNameAccount| bool, a predefinição é TRUE|Estático|Especifica se pretende gerar uma conta com o algoritmo de geração de V1 de nome de utilizador. A partir do Service Fabric versão 6.1; sempre é criada uma conta com a geração da v2. A conta V1 é necessária para atualizações de/para versões que não suportam a geração de V2 (antes da 6.1).|
|MaxCopyOperationThreads | Uint, o padrão é 0 |Dinâmica| O número máximo de ficheiros paralelos ou secundários pode copiar do primário. '0' = = número de núcleos. |
|MaxFileOperationThreads | Uint, a predefinição é 100 |Estático| O número máximo de threads paralelos autorizados a executar FileOperations (copiar/mover) no principal. '0' = = número de núcleos. |
|MaxRequestProcessingThreads | Uint, a predefinição é 200 |Estático|O número máximo de threads paralelos permitida para processar pedidos no principal. '0' = = número de núcleos. |
|MaxSecondaryFileCopyFailureThreshold | Uint, a predefinição é 25|Dinâmica|O número máximo de tentativas de cópia do ficheiro secundário antes de desistir. |
|MaxStoreOperations | Uint, o padrão é 4096 |Estático|O número máximo de operações de transações de arquivo paralelo permitido primário. '0' = = número de núcleos. |
|NamingOperationTimeout |Tempo em segundos, a predefinição é 60 |Dinâmica|Especifique o período de tempo em segundos. O tempo limite para executar a operação de nomenclatura. |
|PrimaryAccountNTLMPasswordSecret | SecureString, a predefinição está vazia |Estático| O segredo de palavra-passe que utilizado como base para a mesma palavra-passe gerada ao utilizar a autenticação NTLM. |
|PrimaryAccountNTLMX509StoreLocation | cadeia de caracteres, predefinido é "LocalMachine"|Estático| A localização do arquivo de X509 certificado utilizado para gerar HMAC no PrimaryAccountNTLMPasswordSecret ao utilizar a autenticação NTLM. |
|PrimaryAccountNTLMX509StoreName | cadeia de caracteres, predefinido é "MY"|Estático| O nome de arquivo de X509 certificado utilizado para gerar HMAC no PrimaryAccountNTLMPasswordSecret ao utilizar a autenticação NTLM. |
|PrimaryAccountNTLMX509Thumbprint | cadeia de caracteres, a predefinição é ""|Estático|O thumbprint de X509 certificado utilizado para gerar HMAC no PrimaryAccountNTLMPasswordSecret ao utilizar a autenticação NTLM. |
|PrimaryAccountType | cadeia de caracteres, a predefinição é "" |Estático|O principal AccountType da entidade de segurança para a ACL a FileStoreService partilhas. |
|PrimaryAccountUserName | cadeia de caracteres, a predefinição é "" |Estático|A conta principal do nome de utilizador do principal de ACL o FileStoreService partilhas. |
|PrimaryAccountUserPassword | SecureString, a predefinição está vazia |Estático|A palavra-passe da conta primária da entidade de segurança para a ACL a FileStoreService partilhas. |
|QueryOperationTimeout | Tempo em segundos, a predefinição é 60 |Dinâmica|Especifique o período de tempo em segundos. O tempo limite para executar a operação de consulta. |
|SecondaryAccountNTLMPasswordSecret | SecureString, a predefinição está vazia |Estático| O segredo de palavra-passe que utilizado como base para a mesma palavra-passe gerada ao utilizar a autenticação NTLM. |
|SecondaryAccountNTLMX509StoreLocation | cadeia de caracteres, predefinido é "LocalMachine" |Estático|A localização do arquivo de X509 certificado utilizado para gerar HMAC no SecondaryAccountNTLMPasswordSecret ao utilizar a autenticação NTLM. |
|SecondaryAccountNTLMX509StoreName | cadeia de caracteres, predefinido é "MY" |Estático|O nome de arquivo de X509 certificado utilizado para gerar HMAC no SecondaryAccountNTLMPasswordSecret ao utilizar a autenticação NTLM. |
|SecondaryAccountNTLMX509Thumbprint | cadeia de caracteres, a predefinição é ""| Estático|O thumbprint de X509 certificado utilizado para gerar HMAC no SecondaryAccountNTLMPasswordSecret ao utilizar a autenticação NTLM. |
|SecondaryAccountType | cadeia de caracteres, a predefinição é ""|Estático| O elemento secundário AccountType da entidade de segurança para a ACL a FileStoreService partilhas. |
|SecondaryAccountUserName | cadeia de caracteres, a predefinição é ""| Estático|A nome de utilizador do principal de ACL de conta secundária o FileStoreService partilhas. |
|SecondaryAccountUserPassword | SecureString, a predefinição está vazia |Estático|A palavra-passe de conta secundária da entidade de segurança para a ACL a FileStoreService partilhas. |
|SecondaryFileCopyRetryDelayMilliseconds|uint, predefinido é 500|Dinâmica|A cópia do ficheiro Repita atraso (em milissegundos).|
|UseChunkContentInTransportMessage|bool, a predefinição é TRUE|Dinâmica|O sinalizador para utilizar a nova versão do protocolo de carregamento introduzida no v6.4. Esta versão de protocolo usa o transporte do service fabric para carregar ficheiros para o armazenamento de imagens que fornece desempenho melhor do que o protocolo SMB utilizado em versões anteriores. |

## <a name="healthmanager"></a>HealthManager
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou descrição breve** |
| --- | --- | --- | --- |
|EnableApplicationTypeHealthEvaluation |Bool, a predefinição é falso |Estático|Política de avaliação do Estado de funcionamento do cluster: Ativar por avaliação de estado de funcionamento do tipo de aplicação. |
|MaxSuggestedNumberOfEntityHealthReports|Int, predefinido é 500 |Dinâmica|O número máximo de estado de funcionamento relatórios que uma entidade pode ter antes de gerar preocupações sobre o estado de funcionamento do watchdog lógica de geração de relatórios. Cada entidade de estado de funcionamento deve para ter um número relativamente pequeno de relatórios de estado de funcionamento. Se a contagem do relatório está mais este número; poderão existir problemas com a implementação de watchdog. Uma entidade com muitos relatórios é sinalizada por meio de um relatório de estado de funcionamento de aviso quando a entidade é avaliada. |

## <a name="healthmanagerclusterhealthpolicy"></a>HealthManager/ClusterHealthPolicy
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou descrição breve** |
| --- | --- | --- | --- |
|ConsiderWarningAsError |Bool, a predefinição é falso |Estático|Política de avaliação do Estado de funcionamento do cluster: avisos são tratados como erros. |
|MaxPercentUnhealthyApplications | int, a predefinição é 0 |Estático|Política de avaliação do Estado de funcionamento do cluster: percentagem máxima de aplicações de mau estado de funcionamento permitido para o cluster seja bom estado de funcionamento. |
|MaxPercentUnhealthyNodes | int, a predefinição é 0 |Estático|Política de avaliação do Estado de funcionamento do cluster: percentagem máxima de nós de mau estado de funcionamento permitido para o cluster seja bom estado de funcionamento. |

## <a name="healthmanagerclusterupgradehealthpolicy"></a>HealthManager/ClusterUpgradeHealthPolicy
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou descrição breve** |
| --- | --- | --- | --- |
|MaxPercentDeltaUnhealthyNodes|Int, a predefinição é 10|Estático|Política de avaliação de atualização do Estado de funcionamento do cluster: percentagem máxima de nós delta de mau estado de funcionamento permitido para o cluster seja bom estado de funcionamento |
|MaxPercentUpgradeDomainDeltaUnhealthyNodes|int, a predefinição é 15|Estático|Política de avaliação de atualização do Estado de funcionamento do cluster: percentagem máxima do delta de mau estado de funcionamento nós num domínio de atualização permitidos para o cluster seja bom estado de funcionamento |

## <a name="hosting"></a>Alojamento
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou descrição breve** |
| --- | --- | --- | --- |
|ActivationMaxFailureCount |É o número inteiro, predefinição 10 |Dinâmica|Número de vezes repete o sistema falha na ativação antes de desistir |
|ActivationMaxRetryInterval |Tempo em segundos, a predefinição é de 300 |Dinâmica|Em cada caso de falha de ativação contínua, o sistema tentará novamente a ativação de até ActivationMaxFailureCount. ActivationMaxRetryInterval Especifica o intervalo de tempo de espera antes de repetir após cada falha de ativação |
|ActivationRetryBackoffInterval |Tempo em segundos, a predefinição é 5 |Dinâmica|Intervalo de término em cada caso de falha de ativação; Em cada caso de falha de ativação contínua, o sistema tentará novamente a ativação de até o MaxActivationFailureCount. O intervalo entre tentativas em cada tente é um produto de falha de ativação contínua e o intervalo de término ativação. |
|ActivationTimeout| Período de tempo, a predefinição é Common::TimeSpan::FromSeconds(180)|Dinâmica| Especifique o período de tempo em segundos. O tempo limite para ativação de aplicativo; a desativação e a atualização. |
|ApplicationHostCloseTimeout| Período de tempo, a predefinição é Common::TimeSpan::FromSeconds(120)|Dinâmica| Especifique o período de tempo em segundos. Quando a saída de recursos de infraestrutura é detetada num self ativado processos; FabricRuntime fecha todas as réplicas no processo de anfitrião (applicationhost) do utilizador. Este é o tempo limite para a operação de fecho. |
|ApplicationUpgradeTimeout| Período de tempo, a predefinição é Common::TimeSpan::FromSeconds(360)|Dinâmica| Especifique o período de tempo em segundos. O tempo limite para a atualização da aplicação. Se o tempo limite é menor do que o implementador de "ActivationTimeout" irá falhar. |
|ContainerServiceArguments|cadeia de caracteres, a predefinição é "-H localhost:2375 -H npipe: / /"|Estático|Service Fabric (SF) gere o daemon do docker (exceto em máquinas de cliente do windows, como o Win10). Esta configuração permite que o usuário especificar argumentos personalizados que devem ser passados para o daemon do docker ao iniciá-los. Quando os argumentos personalizados são especificados, o Service Fabric não passa outros argumentos ao motor do Docker, exceto "– pidfile' argumento. Por conseguinte, os utilizadores não devem especificar "– pidfile' argumento como parte de seus argumentos de cliente. Além disso, os argumentos personalizados devem garantir que o docker daemon escuta no pipe de nome predefinido no Windows (ou o socket de domínio Unix no Linux) para o Service Fabric conseguir comunicar com o mesmo.|
|ContainerServiceLogFileMaxSizeInKb|int, a predefinição é 32768|Estático|Tamanho máximo do ficheiro de registo gerado pelo contentores do docker.  Windows.|
|ContainerImageDownloadTimeout|int, número de segundos, a predefinição é 1200 (20 minutos)|Dinâmica|Número de segundos antes de download da imagem exceder o tempo limite.|
|ContainerImagesToSkip|cadeia de caracteres, nomes de imagem separados pelo caractere de linha vertical, a predefinição é ""|Estático|Nome de um ou mais imagens de contentor que não devem ser eliminados.  Utilizado com o parâmetro de PruneContainerImages.|
|ContainerServiceLogFileNamePrefix|cadeia de caracteres, predefinido é "sfcontainerlogs"|Estático|Prefixo do nome de ficheiro para os ficheiros de registo gerado pelo contentores do docker.  Windows.|
|ContainerServiceLogFileRetentionCount|Int, a predefinição é 10|Estático|Número de ficheiros de registo gerado pelo contentores do docker antes de ficheiros de registo é substituído.  Windows.|
|CreateFabricRuntimeTimeout|Período de tempo, a predefinição é Common::TimeSpan::FromSeconds(120)|Dinâmica| Especifique o período de tempo em segundos. O valor de tempo limite para a sincronização FabricCreateRuntime chamada |
|DefaultContainerRepositoryAccountName|cadeia de caracteres, a predefinição é ""|Estático|Credenciais predefinidas utilizadas em vez das credenciais especificadas em applicationmanifest. XML |
|DefaultContainerRepositoryPassword|cadeia de caracteres, a predefinição é ""|Estático|Credenciais de palavra-passe padrão usadas em vez das credenciais especificadas em applicationmanifest. XML|
|DefaultContainerRepositoryPasswordType|cadeia de caracteres, a predefinição é ""|Estático|Quando a cadeia não vazia, o valor pode ser "Encrypted" ou "SecretsStoreRef".|
|DeploymentMaxFailureCount|Int, a predefinição é 20| Dinâmica|Implementação de aplicação será repetida para tempos de DeploymentMaxFailureCount antes da falha a implementação desse aplicativo no nó.| 
|DeploymentMaxRetryInterval| Período de tempo, a predefinição é Common::TimeSpan::FromSeconds(3600)|Dinâmica| Especifique o período de tempo em segundos. Intervalo de repetição máximo para a implementação. Em cada caso de falha contínuo, o intervalo entre tentativas é calculado como Min (DeploymentMaxRetryInterval; Número de falhas contínua * DeploymentRetryBackoffInterval) |
|DeploymentRetryBackoffInterval| Período de tempo, a predefinição é Common::TimeSpan::FromSeconds(10)|Dinâmica|Especifique o período de tempo em segundos. Intervalo de término da falha de implementação. Em cada caso de falha de implementação contínua, o sistema tentará novamente a implementação da até o MaxDeploymentFailureCount. O intervalo entre tentativas é um produto de falha de implementação contínua e o intervalo de término da implantação. |
|DisableContainers|bool, a predefinição é falso|Estático|Configuração para o desativar contentores - usados em vez de DisableContainerServiceStartOnContainerActivatorOpen que é preterido config |
|DisableDockerRequestRetry|bool, a predefinição é falso |Dinâmica| Por predefinição SF comunica com DD (docker dameon) com um tempo limite de "DockerRequestTimeout" para cada solicitação de http enviado para o mesmo. Se DD não responde dentro deste período de tempo; SF reenvia o pedido se a operação de nível superior ainda tem o tempo restante.  Com o contentor de Hyper-v; DD por vezes, demorar muito mais tempo para abrir o contentor ou desativar. No pedido DD tais casos expire da perspectiva de SF e SF repetem a operação. Por vezes, isso parece adiciona mais pressão no DD. Esta configuração permite desativar esta repetição e aguarde DD responder. |
|EnableActivateNoWindow| bool, a predefinição é falso|Dinâmica| O processo ativado é criado em segundo plano sem qualquer consola. |
|EnableContainerServiceDebugMode|bool, a predefinição é TRUE|Estático|Ativar/desativar o registo para contentores do docker.  Windows.|
|EnableDockerHealthCheckIntegration|bool, a predefinição é TRUE|Estático|Permite a integração de eventos HEALTHCHECK do docker com o relatório de estado de funcionamento do sistema de Service Fabric |
|EnableProcessDebugging|bool, a predefinição é falso|Dinâmica| Permite que a inicialização de hosts de aplicativos sob o depurador |
|EndpointProviderEnabled| bool, a predefinição é falso|Estático| Ativa a gestão de recursos de ponto final por recursos de infraestrutura. Requer a especificação de início e de fim intervalo de portas de aplicação no FabricNode. |
|FabricContainerAppsEnabled| bool, a predefinição é falso|Estático| |
|FirewallPolicyEnabled|bool, a predefinição é falso|Estático| Permite a abertura de portas de firewall para recursos de ponto final com portas explícitas especificadas no ServiceManifest |
|GetCodePackageActivationContextTimeout|Período de tempo, a predefinição é Common::TimeSpan::FromSeconds(120)|Dinâmica|Especifique o período de tempo em segundos. O valor de tempo limite para as chamadas de CodePackageActivationContext. Não se aplica aos serviços do ad-hoc. |
|GovernOnlyMainMemoryForProcesses|bool, a predefinição é falso|Estático|Comportamento predefinido de governação de recursos é colocar o limite especificado na MemoryInMB na quantidade de total de memória (RAM + comutação) que processam o utiliza. Se o limite for excedido; o processo receberá OutOfMemory exceção. Se este parâmetro estiver definido como true; limite será aplicado apenas a quantidade de memória RAM que irá utilizar um processo. Se este limite for excedido; e se esta definição for true; em seguida, sistema operacional será trocar a memória principal para o disco. |
|IPProviderEnabled|bool, a predefinição é falso|Estático|Ativa a gestão de endereços IP. |
|IsDefaultContainerRepositoryPasswordEncrypted|bool, a predefinição é falso|Estático|Se o DefaultContainerRepositoryPassword está ou não ser encriptada.|
|LinuxExternalExecutablePath|cadeia de caracteres, a predefinição é "/ usr/bin /" |Estático|O diretório principal de comandos executáveis externos no nó.|
|NTLMAuthenticationEnabled|bool, a predefinição é falso|Estático| Ativa o suporte para utilizando o NTLM, os pacotes de código que estão em execução com outras utilizadores para que os processos em máquinas possam comunicar de forma segura. |
|NTLMAuthenticationPasswordSecret|SecureString, a predefinição é Common::SecureString("")|Estático|É que tem um encriptada que é utilizado para gerar a palavra-passe para utilizadores NTLM. Tem de ser definida se NTLMAuthenticationEnabled for verdadeira. Validado pelo deployer. |
|NTLMSecurityUsersByX509CommonNamesRefreshInterval|Período de tempo, a predefinição é Common::TimeSpan::FromMinutes(3)|Dinâmica|Especifique o período de tempo em segundos. Definições específicas do ambiente o intervalo periódico no qual alojamento verifica a existência de novos certificados para ser utilizado para a configuração de FileStoreService NTLM. |
|NTLMSecurityUsersByX509CommonNamesRefreshTimeout|Período de tempo, a predefinição é Common::TimeSpan::FromMinutes(4)|Dinâmica| Especifique o período de tempo em segundos. O tempo limite para configurar utilizadores NTLM através de nomes comum do certificado. Os utilizadores NTLM são necessárias para partilhas de FileStoreService. |
|PruneContainerImages|bool, a predefinição é falso|Dinâmica| Remova imagens de contentor de aplicações não utilizadas de nós. Quando um ApplicationType é anulado o registo do cluster do Service Fabric, as imagens de contentor que foram utilizadas por esta aplicação serão removidas em nós em que foi transferido pelo Service Fabric. A eliminação é executado a cada hora, pelo que poderá demorar até uma hora (mais tempo para eliminar a imagem) para imagens a ser removido do cluster.<br>Service Fabric nunca irá transferir ou remover imagens não relacionadas com uma aplicação.  Imagens não relacionadas que foram transferidas manualmente ou caso contrário, tem de ser explicitamente removidas.<br>Imagens que não devem ser eliminadas podem ser especificadas no parâmetro ContainerImagesToSkip.| 
|RegisterCodePackageHostTimeout|Período de tempo, a predefinição é Common::TimeSpan::FromSeconds(120)|Dinâmica| Especifique o período de tempo em segundos. O valor de tempo limite para a chamada de sincronização FabricRegisterCodePackageHost. Isto é aplicável para apenas várias código pacote hosts de aplicativos, como FWP |
|RequestTimeout|Período de tempo, a predefinição é Common::TimeSpan::FromSeconds(30)|Dinâmica| Especifique o período de tempo em segundos. Isso representa o tempo limite para a comunicação entre o processo de recursos de infraestrutura para várias operações relacionadas alojamento, como o registo de fábrica; e de anfitrião de aplicações do utilizador registo de tempo de execução. |
|RunAsPolicyEnabled| bool, a predefinição é falso|Estático| Permite a execução de pacotes de código como um utilizador local que não seja o utilizador sob os recursos de infraestrutura processo está em execução. Para ativar esta política de recursos de infraestrutura tem de executar como sistema ou utilizador que tenha SeAssignPrimaryTokenPrivilege. |
|ServiceFactoryRegistrationTimeout| Período de tempo, a predefinição é Common::TimeSpan::FromSeconds(120)|Dinâmica|Especifique o período de tempo em segundos. O valor de tempo limite para a sincronização chamada ServiceFactory Register (sem estado/com monitorização de estado) |
|ServiceTypeDisableFailureThreshold |Número inteiro, predefinido é 1 |Dinâmica|Este é o limiar para o número de falhas após o qual FailoverManager (FM) é notificado para desativar o tipo de serviço nesse nó e tente um nó diferente para colocação. |
|ServiceTypeDisableGraceInterval|Período de tempo, a predefinição é Common::TimeSpan::FromSeconds(30)|Dinâmica|Especifique o período de tempo em segundos. Intervalo de tempo após o qual o tipo de serviço pode ser desativado |
|ServiceTypeRegistrationTimeout |Tempo em segundos, a predefinição é de 300 |Dinâmica|Tempo máximo permitido para o ServiceType ser registado com recursos de infraestrutura |
|UseContainerServiceArguments|bool, a predefinição é TRUE|Estático|Esta configuração informa ao que aloja a ignorar passando argumentos (especificados na configuração ContainerServiceArguments) para o daemon do docker.|

## <a name="httpgateway"></a>HttpGateway
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou descrição breve** |
| --- | --- | --- | --- |
|ActiveListeners |Uint, a predefinição é 50 |Estático| Número de leituras por oposição a publicar na fila de servidor http. Esse item controla o número de pedidos simultâneos que podem ser concluídas ao HttpGateway. |
|HttpGatewayHealthReportSendInterval |Tempo em segundos, a predefinição é 30 |Estático|Especifique o período de tempo em segundos. O intervalo em que o Http Gateway envia o estado de funcionamento acumulado relatórios para o Gestor de estado de funcionamento. |
|HttpStrictTransportSecurityHeader|cadeia de caracteres, a predefinição é ""|Dinâmica| Especifique o valor de cabeçalho de segurança de transporte HTTP Strict a serem incluídos em cada resposta enviada pelo HttpGateway. Quando definido como cadeia de caracteres vazia; Este cabeçalho não será incluído na resposta de gateway.|
|IsEnabled|Bool, a predefinição é falso |Estático| Ativa/desativa o HttpGateway. HttpGateway está desativada por predefinição. |
|MaxEntityBodySize |Uint, a predefinição é 4194304 |Dinâmica|Fornece o tamanho máximo do corpo que pode esperar de uma solicitação http. Valor predefinido é de 4MB. Httpgateway falhará um pedido, se tiver um corpo de tamanho > Este valor. Tamanho dos segmentos de leitura mínimo é 4096 bytes. Para que isso tem de ser > = 4096. |

## <a name="imagestoreservice"></a>ImageStoreService
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou descrição breve** |
| --- | --- | --- | --- |
|Ativado |Bool, a predefinição é falso |Estático|O sinalizador ativado para ImageStoreService. Predefinição: Falso |
|MinReplicaSetSize | Int, a predefinição é 3 |Estático|O MinReplicaSetSize para ImageStoreService. |
|PlacementConstraints | cadeia de caracteres, a predefinição é "" |Estático| O PlacementConstraints para ImageStoreService. |
|QuorumLossWaitDuration | Tempo em segundos, a predefinição é MaxValue |Estático| Especifique o período de tempo em segundos. O QuorumLossWaitDuration para ImageStoreService. |
|ReplicaRestartWaitDuration | Tempo em segundos, a predefinição é 60.0 * 30 |Estático|Especifique o período de tempo em segundos. O ReplicaRestartWaitDuration para ImageStoreService. |
|StandByReplicaKeepDuration | Tempo em segundos, a predefinição é 3600.0 * 2 |Estático| Especifique o período de tempo em segundos. O StandByReplicaKeepDuration para ImageStoreService. |
|TargetReplicaSetSize | Int, o padrão é 7 |Estático|O TargetReplicaSetSize para ImageStoreService. |

## <a name="ktllogger"></a>KtlLogger
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou descrição breve** |
| --- | --- | --- | --- |
|AutomaticMemoryConfiguration |int, a predefinição é 1 |Dinâmica|Sinalizador que indica se as definições de memória devem ser dinamicamente e automaticamente configuradas. Se a zero, em seguida, as definições de configuração de memória são usadas diretamente e não são alterados com base nas condições de sistema. Se um, em seguida, as definições de memória são configuradas automaticamente e pode ser alterada com base nas condições de sistema. |
|MaximumDestagingWriteOutstandingInKB | int, a predefinição é 0 |Dinâmica|O número de KB para permitir o início de sessão partilhado avançar à frente de registo de dedicado. Utilize 0 para indicar sem limite.
|SharedLogId |cadeia de caracteres, a predefinição é "" |Estático|Guid exclusivo para o contentor de registo partilhado. Utilize "" se utilizar o caminho predefinido na raiz de dados de recursos de infraestrutura. |
|SharedLogPath |cadeia de caracteres, a predefinição é "" |Estático|Nome de ficheiro e caminho para a localização para colocar o contentor de registo partilhado. Utilize "" para utilizar o caminho predefinido na raiz de dados de recursos de infraestrutura. |
|SharedLogSizeInMB |Int, a predefinição é 8192 |Estático|O número de MB para alocar no contentor de registo partilhado. |
|SharedLogThrottleLimitInPercentUsed|int, a predefinição é 0 | Estático | A percentagem de utilização do registo partilhado que será induza limitação. Valor deve ser entre 0 e 100. Um valor de 0 indica que o valor de percentagem de predefinido a utilizar. Um valor de 100 implica sem limitação de todo. Um valor entre 1 e 99 Especifica a percentagem de utilização do log acima ocorre que qualquer limitação; para o exemplo, se o registo partilhado é 10GB e o valor é 90 throttleing irá ocorrer depois de 9GB está em utilização. É recomendado utilizar o valor predefinido.|
|WriteBufferMemoryPoolMaximumInKB | int, a predefinição é 0 |Dinâmica|O número de KB para permitir que o conjunto de memória de memória intermédia de escrita para crescer até. Utilize 0 para indicar sem limite. |
|WriteBufferMemoryPoolMinimumInKB |Int, a predefinição é 8388608 |Dinâmica|O número de KB para alocar inicialmente para o conjunto de memória de memória intermédia de escrita. Utilize 0 para indicar sem limite predefinido deve ser consistente com SharedLogSizeInMB abaixo. |

## <a name="management"></a>Gestão
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou descrição breve** |
| --- | --- | --- | --- |
|AzureStorageMaxConnections | Int, a predefinição é 5000 |Dinâmica|O número máximo de ligações simultâneas para o armazenamento do azure. |
|AzureStorageMaxWorkerThreads | int, a predefinição é 25 |Dinâmica|O número máximo de threads de trabalho em paralelo. |
|AzureStorageOperationTimeout | Tempo em segundos, a predefinição é de 6000 |Dinâmica|Especifique o período de tempo em segundos. Tempo limite para a conclusão da operação xstore. |
|CleanupApplicationPackageOnProvisionSuccess|bool, a predefinição é falso |Dinâmica|Esta configuração ativa ou desativa a limpeza automática de pacote de aplicação fornecimento com êxito. |
|DisableChecksumValidation | Bool, a predefinição é falso |Estático| Esta configuração permite-nos ativar ou desativar a validação de soma de verificação durante o aprovisionamento de aplicação. |
|DisableServerSideCopy | Bool, a predefinição é falso |Estático|Esta configuração ativa ou desativa a cópia do lado do servidor de pacote de aplicação no ImageStore durante o aprovisionamento de aplicação. |
|ImageCachingEnabled | Bool, a predefinição é verdadeiro |Estático|Esta configuração permite-nos ativar ou desativar a colocação em cache. |
|ImageStoreConnectionString |SecureString |Estático|Cadeia de ligação para a raiz para ImageStore. |
|ImageStoreMinimumTransferBPS | Int, o padrão é 1024 |Dinâmica|A taxa de transferência mínimos entre o cluster e ImageStore. Este valor é utilizado para determinar o tempo limite ao aceder a ImageStore externo. Altere este valor apenas se a latência entre o cluster e ImageStore é elevada para permitir que mais tempo para o cluster transferir a partir do ImageStore externo. |

## <a name="metricactivitythresholds"></a>MetricActivityThresholds
| **Parâmetro** | **Valores permitidos** |**Política de atualização**| **Documentação de orientação ou descrição breve** |
| --- | --- | --- | --- |
|PropertyGroup|KeyIntegerValueMap, a predefinição é nenhum|Dinâmica|Determina o conjunto de MetricActivityThresholds para as métricas do cluster. Balanceamento funcionará se maxNodeLoad for superior a MetricActivityThresholds. Para as métricas de desfragmentação define a quantidade de carga igual a ou abaixo do qual o Service Fabric irá considerar o nó vazio |

## <a name="metricbalancingthresholds"></a>MetricBalancingThresholds
| **Parâmetro** | **Valores permitidos** |**Política de atualização**| **Documentação de orientação ou descrição breve** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, a predefinição é nenhum|Dinâmica|Determina o conjunto de MetricBalancingThresholds para as métricas do cluster. Balanceamento funcionará se maxNodeLoad/minNodeLoad for superior a MetricBalancingThresholds. Desfragmentação funcionará se maxNodeLoad/minNodeLoad em, pelo menos, um FD ou UD for menor que MetricBalancingThresholds. |

## <a name="namingservice"></a>NamingService
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou descrição breve** |
| --- | --- | --- | --- |
|GatewayServiceDescriptionCacheLimit |int, a predefinição é 0 |Estático|O número máximo de entradas mantidas no cache de descrição do serviço LRU no Gateway de nomenclatura (definido como 0 para nenhum limite). |
|MaxClientConnections |Int, a predefinição é 1000 |Dinâmica|O máximo permitido de número de ligações de cliente por gateway. |
|MaxFileOperationTimeout |Tempo em segundos, a predefinição é 30 |Dinâmica|Especifique o período de tempo em segundos. Tempo limite máximo permitido para a operação de serviço de armazenamento de ficheiros. Pedidos de especificar o tempo limite serão rejeitados. |
|MaxIndexedEmptyPartitions |Int, a predefinição é 1000 |Dinâmica|O número máximo de partições vazias que irá permanecer indexados no cache de notificação para a sincronização de clientes a restabelecer ligação. Quaisquer partições vazias acima esse número serão removidas do índice por pesquisa versão ordem ascendente. Os clientes a restabelecer ligação ainda pode sincronizar e receber atualizações em falta partição vazia; mas o protocolo de sincronização se torna mais caro. |
|MaxMessageSize |Int, a predefinição é 4\*1024\*1024 |Estático|O tamanho máximo para a comunicação de nó de cliente ao utilizar a atribuição de nomes. Alleviation de ataque DOS; valor predefinido é de 4MB. |
|MaxNamingServiceHealthReports | Int, a predefinição é 10 |Dinâmica|O número máximo de operações lentas que armazenam de nomenclatura de mau estado de funcionamento de relatórios do serviço de uma só vez. Se for 0; todas as operações lentas são enviadas. |
|MaxOperationTimeout |Tempo em segundos, a predefinição é de 600 |Dinâmica|Especifique o período de tempo em segundos. Tempo limite máximo permitido para operações de cliente. Pedidos de especificar o tempo limite serão rejeitados. |
|MaxOutstandingNotificationsPerClient |Int, a predefinição é 1000 |Dinâmica|O número máximo de notificações pendentes antes de um registo de cliente é forçado a fechar pelo gateway. |
|MinReplicaSetSize | Int, a predefinição é 3 |Não Permitido| O número mínimo de réplicas do serviço de nomenclatura necessários para gravar no concluir uma atualização. Se existirem menos réplicas que isto ativos no sistema o sistema de confiabilidade nega atualizações para o Store do serviço de nomenclatura de até que as réplicas são restauradas. Este valor deve ser mais do que o TargetReplicaSetSize nunca. |
|PartitionCount |Int, a predefinição é 3 |Não Permitido|O número de partições do serviço de nomenclatura de armazena a ser criada. Cada partição é proprietário de uma chave de partição única que corresponde ao respetivo índice; por isso, as chaves de partição [0; PartitionCount) existe. Aumentar o número de aumentos de partições do serviço de nomenclatura escala a que o serviço de nomenclatura pode realizar ao reduzir a quantidade média de dados retidos pela qualquer réplica backup definido; a um custo de uma maior utilização dos recursos (desde PartitionCount * ReplicaSetSize réplicas de serviço devem ser mantidas).|
|PlacementConstraints | cadeia de caracteres, a predefinição é "" |Não Permitido| Restrição de posicionamento para o serviço de nomenclatura. |
|QuorumLossWaitDuration | Tempo em segundos, a predefinição é MaxValue |Não Permitido| Especifique o período de tempo em segundos. Quando um serviço de nomenclatura entra em perda de quórum; Este temporizador inicia. Quando este expirar o FM considerará as réplicas baixo como perdido; e tentar recuperar o quórum. Não é que isso pode resultar em perda de dados. |
|RepairInterval | Tempo em segundos, a predefinição é 5 |Estático| Especifique o período de tempo em segundos. Intervalo no qual será iniciada a nomenclatura reparação de inconsistência entre o proprietário de autoridade e o proprietário do nome. |
|ReplicaRestartWaitDuration | Tempo em segundos, a predefinição é (60.0 * 30)|Não Permitido| Especifique o período de tempo em segundos. Quando uma réplica de nomenclatura serviço fica inativo; Este temporizador inicia. Quando este expirar o FM começará a substituir as réplicas que são para baixo (ele não ainda considerá-las perdido). |
|ServiceDescriptionCacheLimit | int, a predefinição é 0 |Estático| O número máximo de entradas mantidas no cache de descrição do serviço LRU no serviço de nomenclatura de Store (definido como 0 para nenhum limite). |
|ServiceNotificationTimeout |Tempo em segundos, a predefinição é 30 |Dinâmica|Especifique o período de tempo em segundos. O tempo limite utilizado ao entregar notificações de serviço ao cliente. |
|StandByReplicaKeepDuration | Tempo em segundos, a predefinição é 3600.0 * 2 |Não Permitido| Especifique o período de tempo em segundos. Quando uma réplica Naming Service voltar atrás de um Estado para baixo; Pode já ter foi substituído. Este temporizador determina quanto o FM irá manter a réplica em modo de espera antes de descartá-la. |
|TargetReplicaSetSize |Int, o padrão é 7 |Não Permitido|Define o número de réplica para cada partição do armazenamento de serviço de nomenclatura. Aumentar o número de conjuntos de réplicas aumenta o nível de confiabilidade para obter as informações na Store de serviço de nomenclatura; diminuir a alteração que as informações serão perdidas devido a falhas de nó a um custo de aumento de carga nos recursos de infraestrutura do Windows e a quantidade de tempo que demora a executar atualizações para os dados de nomenclatura.|

## <a name="nodebufferpercentage"></a>NodeBufferPercentage
| **Parâmetro** | **Valores permitidos** |**Política de atualização**| **Documentação de orientação ou descrição breve** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, a predefinição é nenhum|Dinâmica|Percentagem de capacidade do nó por nome da métrica; utilizado como uma memória intermédia para manter algum lugar gratuito num nó para o caso de ativação pós-falha. |

## <a name="nodecapacities"></a>NodeCapacities
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou descrição breve** |
| --- | --- | --- | --- |
|PropertyGroup |NodeCapacityCollectionMap |Estático|Uma coleção de nó as capacidades para métricas diferentes. |

## <a name="nodedomainids"></a>NodeDomainIds
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou descrição breve** |
| --- | --- | --- | --- |
|PropertyGroup |NodeFaultDomainIdCollection |Estático|Descreve os domínios de falha, que um nó pertence. O domínio de falhas é definido através de um URI que descreve a localização do nó no Centro de dados.  URIs de domínio de falha estão no formato fd: / fd/seguido de um segmento de caminho do URI.|
|UpgradeDomainId |cadeia de caracteres, a predefinição é "" |Estático|Descreve um nó pertence o domínio de atualização. |

## <a name="nodeproperties"></a>NodeProperties
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou descrição breve** |
| --- | --- | --- | --- |
|PropertyGroup |NodePropertyCollectionMap |Estático|Uma coleção de pares de chave-valor de cadeia de carateres para propriedades de nó. |

## <a name="paas"></a>Paas
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou descrição breve** |
| --- | --- | --- | --- |
|ClusterId |cadeia de caracteres, a predefinição é "" |Não Permitido|Arquivo utilizado por recursos de infraestrutura para proteção da configuração de certificado X509. |

## <a name="performancecounterlocalstore"></a>PerformanceCounterLocalStore
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou descrição breve** |
| --- | --- | --- | --- |
|Contadores |String | Dinâmica |Lista separada por vírgulas de contadores de desempenho a recolher. |
|IsEnabled |Bool, a predefinição é verdadeiro | Dinâmica |Sinalizador indica se a recolha do contador de desempenho no nó local está ativada. |
|MaxCounterBinaryFileSizeInMB |int, a predefinição é 1 | Dinâmica |Tamanho máximo (em MB) de cada arquivo binário do contador de desempenho. |
|NewCounterBinaryFileCreationIntervalInMinutes |Int, a predefinição é 10 | Dinâmica |Intervalo máximo (em segundos) após o qual é criado um novo ficheiro binário de contador de desempenho. |
|SamplingIntervalInSeconds |Int, a predefinição é 60 | Dinâmica |Intervalo de amostragem para contadores de desempenho a ser recolhidos. |

## <a name="placementandloadbalancing"></a>PlacementAndLoadBalancing
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou descrição breve** |
| --- | --- | --- | --- |
|AffinityConstraintPriority | int, a predefinição é 0 | Dinâmica|Determina a prioridade de restrição de afinidade: 0: Disco rígido; 1: Forma recuperável; negativo: Ignore. |
|ApplicationCapacityConstraintPriority | int, a predefinição é 0 | Dinâmica|Determina a prioridade de restrição de capacidade: 0: Disco rígido; 1: Forma recuperável; negativo: Ignore. |
|AutoDetectAvailableResources|bool, a predefinição é TRUE|Estático|Esta configuração irá acionar a deteção automática de recursos disponíveis no nó (CPU e memória) quando esta configuração está definida como true - iremos ler as capacidades reais e corrija-os se o utilizador especificado as capacidades de nó incorreto ou não defini-los em todos os se esta configuração é definida como falso - iremos  rastrear um aviso de que o utilizador especificado as capacidades de nó incorreto; mas não corrigiremos-los; o que significa que o utilizador quer que as capacidades especificadas como > que o nó tem realmente ou se as capacidades são indefinidas; ele presumirá capacidade ilimitada |
|BalancingDelayAfterNewNode | Tempo em segundos, a predefinição é 120 |Dinâmica|Especifique o período de tempo em segundos. Não inicie o balanceamento de atividades durante este período depois de adicionar um novo nó. |
|BalancingDelayAfterNodeDown | Tempo em segundos, a predefinição é 120 |Dinâmica|Especifique o período de tempo em segundos. Não inicie o balanceamento de atividades durante este período após um nó de evento para baixo. |
|CapacityConstraintPriority | int, a predefinição é 0 | Dinâmica|Determina a prioridade de restrição de capacidade: 0: Disco rígido; 1: Forma recuperável; negativo: Ignore. |
|ConsecutiveDroppedMovementsHealthReportLimit | Int, a predefinição é 20 | Dinâmica|Define o número de vezes consecutivas que emitido ResourceBalancer movimentos são ignorados antes de diagnóstico é conduzido e são emitidos avisos de estado de funcionamento. Negativo: Não existem avisos emitida sob essa condição. |
|ConstraintFixPartialDelayAfterNewNode | Tempo em segundos, a predefinição é 120 |Dinâmica| Especifique o período de tempo em segundos. DDo não FaultDomain de corrigir e violações de restrição de UpgradeDomain durante este período depois de adicionar um novo nó. |
|ConstraintFixPartialDelayAfterNodeDown | Tempo em segundos, a predefinição é 120 |Dinâmica| Especifique o período de tempo em segundos. Fazer não as violações de restrição FaultDomain corrigir e UpgradeDomain durante este período após um nó de evento para baixo. |
|ConstraintViolationHealthReportLimit | Int, a predefinição é 50 |Dinâmica| Define o número de vezes a réplica de violação de restrição tem de ser persistentemente não corrigidos antes de diagnóstico é conduzido e os relatórios de estado são emitidos. |
|DetailedConstraintViolationHealthReportLimit | Int, a predefinição é 200 |Dinâmica| Define o número de vezes a réplica de violação de restrição tem de ser persistentemente não corrigidos antes de diagnóstico é conduzido e detalhadas são emitidos relatórios de estado de funcionamento. |
|DetailedDiagnosticsInfoListLimit | int, a predefinição é 15 |Dinâmica| Define o número de entradas de diagnóstico (com informações detalhadas) por restrição para incluir antes da truncagem no diagnóstico.|
|DetailedNodeListLimit | int, a predefinição é 15 |Dinâmica| Define o número de nós por restrição para incluir antes da truncagem nos relatórios de réplica Unplaced. |
|DetailedPartitionListLimit | int, a predefinição é 15 |Dinâmica| Define o número de partições por entrada de diagnóstico para uma restrição incluir antes da truncagem no diagnóstico. |
|DetailedVerboseHealthReportLimit | Int, a predefinição é 200 | Dinâmica|Define o número de vezes que uma réplica unplaced tem de ser persistentemente unplaced antes de relatórios de estado de funcionamento detalhadas são emitidos. |
|FaultDomainConstraintPriority | int, a predefinição é 0 |Dinâmica| Determina a prioridade de restrição de domínio de falhas: 0: Disco rígido; 1: Forma recuperável; negativo: Ignore. |
|GlobalMovementThrottleCountingInterval | Tempo em segundos, a predefinição é de 600 |Estático| Especifique o período de tempo em segundos. Indica o comprimento do intervalo passado para o qual pretende controlar por Movimentos de réplica de domínio (utilizados juntamente com GlobalMovementThrottleThreshold). Pode ser definido como 0 para ignorar completamente a limitação global. |
|GlobalMovementThrottleThreshold | Uint, a predefinição é 1000 |Dinâmica| Número máximo de movimentos permitido na fase de balanceamento no intervalo nos últimos indicado pelo GlobalMovementThrottleCountingInterval. |
|GlobalMovementThrottleThresholdForBalancing | Uint, o padrão é 0 | Dinâmica|Número máximo de movimentos permitido na fase de balanceamento no intervalo nos últimos indicado pelo GlobalMovementThrottleCountingInterval. 0 indica sem limite. |
|GlobalMovementThrottleThresholdForPlacement | Uint, o padrão é 0 |Dinâmica| Número máximo de movimentos permitido na fase de colocação no intervalo nos últimos indicado pelo GlobalMovementThrottleCountingInterval.0 indica sem limite.|
|GlobalMovementThrottleThresholdPercentage|Double, o padrão é 0|Dinâmica|Número máximo de movimentos total permitido nas fases de balanceamento e a colocação (expressados em percentagem do número total de réplicas do cluster) no intervalo nos últimos indicado pelo GlobalMovementThrottleCountingInterval. 0 indica sem limite. Se ambos os isso e GlobalMovementThrottleThreshold são especificados; em seguida, o limite mais Conservadora é utilizado.|
|GlobalMovementThrottleThresholdPercentageForBalancing|Double, o padrão é 0|Dinâmica|Número máximo de movimentos permitido na fase de balanceamento de mensagens em fila (expressa em percentagem do número total de réplicas no PLB) no intervalo nos últimos indicado pelo GlobalMovementThrottleCountingInterval. 0 indica sem limite. Se ambos os isso e GlobalMovementThrottleThresholdForBalancing são especificados; em seguida, o limite mais Conservadora é utilizado.|
|InBuildThrottlingAssociatedMetric | cadeia de caracteres, a predefinição é "" |Estático| O nome métrico associado para esta limitação. |
|InBuildThrottlingEnabled | Bool, a predefinição é falso |Dinâmica| Determine se a limitação na compilação está ativada. |
|InBuildThrottlingGlobalMaxValue | int, a predefinição é 0 |Dinâmica|O número máximo de réplicas de na compilação permitido globalmente. |
|InterruptBalancingForAllFailoverUnitUpdates | Bool, a predefinição é falso | Dinâmica|Determina se a qualquer tipo de atualização de unidade de ativação pós-falha deve interrupção rápida ou lenta balanceamento executar. Com especificado balanceamento "false" execute será interrompido se FailoverUnit: é criado/eliminado; tem em falta réplicas; alterar a localização de réplica primária ou alterado número de réplicas. Balanceamento de execução não será interrompido em outros casos - se FailoverUnit: tem réplicas Extras; alterar o sinalizador qualquer réplica; alterar apenas a versão de partição ou qualquer outro caso. |
|MinConstraintCheckInterval | Tempo em segundos, a predefinição é 1 |Dinâmica| Especifique o período de tempo em segundos. Define a quantidade mínima de tempo que deve passar antes rodadas de verificação de dois restrição consecutivos. |
|MinLoadBalancingInterval | Tempo em segundos, a predefinição é 5 |Dinâmica| Especifique o período de tempo em segundos. Define a quantidade mínima de tempo que deve passar antes de dois ciclos de balanceamento consecutivos. |
|MinPlacementInterval | Tempo em segundos, a predefinição é 1 |Dinâmica| Especifique o período de tempo em segundos. Define a quantidade mínima de tempo que deve passar antes de dois ciclos de posicionamento consecutivos. |
|MoveExistingReplicaForPlacement | Bool, a predefinição é verdadeiro |Dinâmica|Definição que determina se mover a réplica já existente durante o posicionamento. |
|MovementPerPartitionThrottleCountingInterval | Tempo em segundos, a predefinição é de 600 |Estático| Especifique o período de tempo em segundos. Indica o comprimento do intervalo passado para o qual pretende controlar movimentos de réplica para cada partição (utilizado juntamente com MovementPerPartitionThrottleThreshold). |
|MovementPerPartitionThrottleThreshold | Uint, a predefinição é 50 |Dinâmica| Nenhum movimento relacionados com o balanceamento ocorrerá para uma partição, se o número de movimentos relacionados para réplicas dessa partição de balanceamento tiver atingido ou excedido MovementPerFailoverUnitThrottleThreshold no intervalo nos últimos indicado pelo MovementPerPartitionThrottleCountingInterval. |
|MoveParentToFixAffinityViolation | Bool, a predefinição é falso |Dinâmica| Definição que determina se as réplicas principal podem ser movidas para corrigir as restrições de afinidade.|
|PartiallyPlaceServices | Bool, a predefinição é verdadeiro |Dinâmica| Determina se todas as réplicas do serviço em cluster serão colocadas "tudo ou nada" fornecido limitados nós adequado para eles.|
|PlaceChildWithoutParent | Bool, a predefinição é verdadeiro | Dinâmica|Definição que determina se a réplica do serviço de subordinados pode ser colocado se nenhuma réplica principal está ativo. |
|PlacementConstraintPriority | int, a predefinição é 0 | Dinâmica|Determina a prioridade de restrição de posicionamento: 0: Disco rígido; 1: Forma recuperável; negativo: Ignore. |
|PlacementConstraintValidationCacheSize | Int, a predefinição é 10000 |Dinâmica| Limita o tamanho da tabela utilizado para rápido de validação e de expressões de restrição de colocação em cache. |
|PlacementSearchTimeout | Tempo em segundos, a predefinição é 0,5 |Dinâmica| Especifique o período de tempo em segundos. Quando colocar os serviços; Pesquisar no máximo essa longa antes de devolver um resultado. |
|PLBRefreshGap | Tempo em segundos, a predefinição é 1 |Dinâmica| Especifique o período de tempo em segundos. Define a quantidade mínima de tempo que deve passar antes de PLB atualiza o estado novamente. |
|PreferredLocationConstraintPriority | Int, a predefinição é 2| Dinâmica|Determina a prioridade de restrição de localização preferencial: 0: Disco rígido; 1: Soft; 2: Otimização; negativo: Ignorar |
|PreferUpgradedUDs|bool, a predefinição é TRUE|Dinâmica|Se e desativar a lógica que prefere a mover para já atualizado UDs.|
|PreventTransientOvercommit | Bool, a predefinição é falso | Dinâmica|Determina a deve PLB imediatamente contar com recursos que serão liberados pelas movimentações iniciadas. Por padrão. PLB pode iniciar a movimentação terminar e mudança no mesmo nó que pode criar transitório sobreconsolidar. Definir esse parâmetro como true, irá impedir que esses tipos de overcommits e que serão de desfragmentação de sob demanda (também conhecido como placementWithMove) desativada. |
|ScaleoutCountConstraintPriority | int, a predefinição é 0 |Dinâmica| Determina a prioridade de restrição de contagem de aumento horizontal: 0: Disco rígido; 1: Forma recuperável; negativo: Ignore. |
|SwapPrimaryThrottlingAssociatedMetric | cadeia de caracteres, a predefinição é ""|Estático| O nome métrico associado para esta limitação. |
|SwapPrimaryThrottlingEnabled | Bool, a predefinição é falso|Dinâmica| Determine se a limitação de comutação primário está ativada. |
|SwapPrimaryThrottlingGlobalMaxValue | int, a predefinição é 0 |Dinâmica| O número máximo de réplicas de comutação primário permitido globalmente. |
|TraceCRMReasons |Bool, a predefinição é verdadeiro |Dinâmica|Especifica se pretende rastrear motivos para CRM emitido movimentos para o canal de eventos operacionais. |
|UpgradeDomainConstraintPriority | int, a predefinição é 1| Dinâmica|Determina a prioridade de restrição de domínio de atualização: 0: Disco rígido; 1: Forma recuperável; negativo: Ignore. |
|UseMoveCostReports | Bool, a predefinição é falso | Dinâmica|Instrui o LB para ignorar o elemento de custo da função de classificação; resultando número potencialmente grande de movimentações para melhor equilibrada colocação. |
|UseSeparateSecondaryLoad | Bool, a predefinição é verdadeiro | Dinâmica|Definição que determina se utilizar a carga secundária diferente. |
|ValidatePlacementConstraint | Bool, a predefinição é verdadeiro |Dinâmica| Especifica se é ou não a expressão de PlacementConstraint para um serviço é validada quando ServiceDescription um serviço é atualizado. |
|VerboseHealthReportLimit | Int, a predefinição é 20 | Dinâmica|Define o número de vezes que uma réplica têm de passar unplaced antes de um aviso do Estado de funcionamento é comunicado para a mesma (se o relatório de estado de funcionamento verboso está ativado). |

## <a name="reconfigurationagent"></a>ReconfigurationAgent
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou descrição breve** |
| --- | --- | --- | --- |
|ApplicationUpgradeMaxReplicaCloseDuration | Tempo em segundos, a predefinição é de 900 |Dinâmica|Especifique o período de tempo em segundos. Feche a duração para o qual o sistema esperará antes de terminar os hosts de serviço com réplicas que está bloqueada no durante a atualização do aplicativo.|
|FabricUpgradeMaxReplicaCloseDuration | Tempo em segundos, a predefinição é de 900 |Dinâmica| Especifique o período de tempo em segundos. Feche a duração para o qual o sistema esperará antes de terminar os hosts de serviço com réplicas que está bloqueada no durante a atualização do fabric. |
|GracefulReplicaShutdownMaxDuration|Período de tempo, a predefinição é Common::TimeSpan::FromSeconds(120)|Dinâmica|Especifique o período de tempo em segundos. A duração para o qual o sistema esperará antes de terminar os hosts de serviço com réplicas que empacamos em Fechar. Se este valor é definido como 0, as réplicas não serão instruídas a fechar.|
|NodeDeactivationMaxReplicaCloseDuration | Tempo em segundos, a predefinição é de 900 |Dinâmica|Especifique o período de tempo em segundos. Feche a duração para o qual o sistema esperará antes de terminar os hosts de serviço com réplicas que está bloqueada no durante a desativação de nó. |
|PeriodicApiSlowTraceInterval | Tempo em segundos, a predefinição é 5 minutos |Dinâmica| Especifique o período de tempo em segundos. PeriodicApiSlowTraceInterval define o intervalo durante o qual irão ser retraced chamadas de API lentas pelo monitor de API. |
|ReplicaChangeRoleFailureRestartThreshold|Int, a predefinição é 10|Dinâmica| Número inteiro. Especifique o número de falhas da API durante a promoção primária após o qual será aplicada a ação de atenuação automática (réplica reiniciar). |
|ReplicaChangeRoleFailureWarningReportThreshold|int, a predefinição é 2147483647|Dinâmica| Número inteiro. Especifique o número de falhas da API durante a promoção primária após o qual o relatório de estado de funcionamento de aviso será gerado.|
|ServiceApiHealthDuration | Tempo em segundos, a predefinição é 30 minutos |Dinâmica| Especifique o período de tempo em segundos. ServiceApiHealthDuration define o tempo em que podemos esperar para uma API de serviço a ser executada antes de que relatamos-mau estado de funcionamento. |
|ServiceReconfigurationApiHealthDuration | Tempo em segundos, a predefinição é 30 |Dinâmica| Especifique o período de tempo em segundos. ServiceReconfigurationApiHealthDuration define o tempo em que podemos esperar para uma API de serviço a ser executada antes elaboramos relatórios de mau estado de funcionamento. Isto aplica-se para chamadas API que afetam a disponibilidade.|

## <a name="replication"></a>Replicação
| **Parâmetro** | **Valores permitidos** | **Política de atualização**| **Documentação de orientação ou descrição breve** |
| --- | --- | --- | --- |
|BatchAcknowledgementInterval|Período de tempo, a predefinição é Common::TimeSpan::FromMilliseconds(15)|Estático|Especifique o período de tempo em segundos. Determina a quantidade de tempo que o replicator aguarda a após o recebimento de uma operação antes de enviar uma confirmação de volta. Outras operações recebidas durante este período de tempo terão suas confirmações enviadas novamente numa única mensagem -> reduzindo o tráfego de rede, mas potencialmente reduzindo a produtividade do replicador.|
|MaxCopyQueueSize|uint, a predefinição é de 1024|Estático|Este é o máximo valor define o tamanho inicial para a fila que mantém as operações de replicação. Tenha em atenção que tem de ser uma potência de 2. Se durante o tempo de execução, a fila aumenta para esta operação de tamanho ficará limitada entre os replicators primários e secundários.|
|MaxPrimaryReplicationQueueMemorySize|Uint, o padrão é 0|Estático|Este é o valor máximo da fila de replicação primária em bytes.|
|MaxPrimaryReplicationQueueSize|uint, a predefinição é de 1024|Estático|Este é o número máximo de operações que pode existir na fila de replicação primária. Tenha em atenção que tem de ser uma potência de 2.|
|MaxReplicationMessageSize|uint, a predefinição é 52428800|Estático|Tamanho da mensagem máximo de operações de replicação. A predefinição é 50MB.|
|MaxSecondaryReplicationQueueMemorySize|Uint, o padrão é 0|Estático|Este é o valor máximo da fila de replicação secundário em bytes.|
|MaxSecondaryReplicationQueueSize|uint, a predefinição é de 2048|Estático|Este é o número máximo de operações que pode existir na fila de replicação secundário. Tenha em atenção que tem de ser uma potência de 2.|
|QueueHealthMonitoringInterval|Período de tempo, a predefinição é Common::TimeSpan::FromSeconds(30)|Estático|Especifique o período de tempo em segundos. Este valor determina o período de tempo utilizado pelo replicador para monitorar qualquer eventos de estado de funcionamento de aviso/erro nas filas de operação de replicação. Um valor "0" desativa a monitorização de estado de funcionamento |
|QueueHealthWarningAtUsagePercent|uint, predefinida é 80|Estático|Este valor determina a utilização da fila de replicação (em percentagem) após o qual relatamos aviso sobre a utilização elevada da fila. Podemos fazê-lo após um intervalo de tolerância de QueueHealthMonitoringInterval. Se a utilização de fila cair abaixo essa porcentagem no intervalo de tolerância|
|ReplicatorAddress|cadeia de caracteres, predefinido é "localhost:0"|Estático|O ponto final na forma de uma cadeia de caracteres - o "IP: porta" que é utilizada pelo replicador de recursos de infraestrutura do Windows para estabelecer ligações com outras réplicas para operações de envio/receção.|
|ReplicatorListenAddress|cadeia de caracteres, predefinido é "localhost:0"|Estático|O ponto final na forma de uma cadeia de caracteres - o "IP: porta" que é utilizada pelo replicador de recursos de infraestrutura do Windows para receber operações de outras réplicas.|
|ReplicatorPublishAddress|cadeia de caracteres, predefinido é "localhost:0"|Estático|O ponto final na forma de uma cadeia de caracteres - o "IP: porta" que é utilizada pelo replicador de recursos de infraestrutura do Windows para enviar operações para outras réplicas.|
|RetryInterval|Período de tempo, a predefinição é Common::TimeSpan::FromSeconds(5)|Estático|Especifique o período de tempo em segundos. Quando uma operação é perdida ou rejeitada este temporizador determina a frequência com que o replicator voltará a tentar enviar a operação.|

## <a name="resourcemonitorservice"></a>ResourceMonitorService
| **Parâmetro** | **Valores permitidos** | **Política de atualização**| **Documentação de orientação ou descrição breve** |
| --- | --- | --- | --- |
|IsEnabled|bool, a predefinição é falso |Estático|Controla se o serviço está ativado no cluster ou não. |

## <a name="runas"></a>RunAs
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou descrição breve** |
| --- | --- | --- | --- |
|RunAsAccountName |cadeia de caracteres, a predefinição é "" |Dinâmica|Indica o nome da conta RunAs. Isto só é necessário para a conta "Utilizador_do_domínio" ou "ManagedServiceAccount" tipo. Os valores válidos são "domínio \ utilizador" ou "user@domain". |
|RunAsAccountType|cadeia de caracteres, a predefinição é "" |Dinâmica|Indica o tipo de conta RunAs. Isto é necessário para qualquer RunAs secção os valores válidos são "Utilizador_do_domínio/NetworkService/ManagedServiceAccount/LocalSystem".|
|RunAsPassword|cadeia de caracteres, a predefinição é "" |Dinâmica|Indica a senha da conta RunAs. Isto só é necessário para o tipo de conta de "Utilizador_do_domínio". |

## <a name="runasdca"></a>RunAs_DCA
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou descrição breve** |
| --- | --- | --- | --- |
|RunAsAccountName |cadeia de caracteres, a predefinição é "" |Dinâmica|Indica o nome da conta RunAs. Isto só é necessário para a conta "Utilizador_do_domínio" ou "ManagedServiceAccount" tipo. Os valores válidos são "domínio \ utilizador" ou "user@domain". |
|RunAsAccountType|cadeia de caracteres, a predefinição é "" |Dinâmica|Indica o tipo de conta RunAs. Isto é necessário para qualquer RunAs secção os valores válidos são "Utilizador_local/Utilizador_do_domínio/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunAsPassword|cadeia de caracteres, a predefinição é "" |Dinâmica|Indica a senha da conta RunAs. Isto só é necessário para o tipo de conta de "Utilizador_do_domínio". |

## <a name="runasfabric"></a>RunAs_Fabric
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou descrição breve** |
| --- | --- | --- | --- |
|RunAsAccountName |cadeia de caracteres, a predefinição é "" |Dinâmica|Indica o nome da conta RunAs. Isto só é necessário para a conta "Utilizador_do_domínio" ou "ManagedServiceAccount" tipo. Os valores válidos são "domínio \ utilizador" ou "user@domain". |
|RunAsAccountType|cadeia de caracteres, a predefinição é "" |Dinâmica|Indica o tipo de conta RunAs. Isto é necessário para qualquer RunAs secção os valores válidos são "Utilizador_local/Utilizador_do_domínio/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunAsPassword|cadeia de caracteres, a predefinição é "" |Dinâmica|Indica a senha da conta RunAs. Isto só é necessário para o tipo de conta de "Utilizador_do_domínio". |

## <a name="runashttpgateway"></a>RunAs_HttpGateway
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou descrição breve** |
| --- | --- | --- | --- |
|RunAsAccountName |cadeia de caracteres, a predefinição é "" |Dinâmica|Indica o nome da conta RunAs. Isto só é necessário para a conta "Utilizador_do_domínio" ou "ManagedServiceAccount" tipo. Os valores válidos são "domínio \ utilizador" ou "user@domain". |
|RunAsAccountType|cadeia de caracteres, a predefinição é "" |Dinâmica|Indica o tipo de conta RunAs. Isto é necessário para qualquer RunAs secção os valores válidos são "Utilizador_local/Utilizador_do_domínio/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunAsPassword|cadeia de caracteres, a predefinição é "" |Dinâmica|Indica a senha da conta RunAs. Isto só é necessário para o tipo de conta de "Utilizador_do_domínio". |

## <a name="security"></a>Segurança
| **Parâmetro** | **Valores permitidos** |**Política de atualização**| **Documentação de orientação ou descrição breve** |
| --- | --- | --- | --- |
|AADCertEndpointFormat|cadeia de caracteres, a predefinição é ""|Estático|AAD Cert ponto final de formato, Azure Commercial, do padrão especificado para o ambiente de não-padrão, como o Azure Government "https://login.microsoftonline.us/{0}/federationmetadata/2007-06/federationmetadata.xml" |
|AADClientApplication|cadeia de caracteres, a predefinição é ""|Estático|Nome da aplicação cliente nativo ou ID que representam os clientes de recursos de infraestrutura |
|AADClusterApplication|cadeia de caracteres, a predefinição é ""|Estático|Nome da aplicação Web API ou o ID que representa o cluster |
|AADLoginEndpoint|cadeia de caracteres, a predefinição é ""|Estático|AAD início de sessão do ponto de extremidade, padrão do Azure comercial, especificado para o ambiente de não-padrão, como o Azure Government "https://login.microsoftonline.us" |
|AADTenantId|cadeia de caracteres, a predefinição é ""|Estático|ID do inquilino (GUID) |
|AdminClientCertThumbprints|cadeia de caracteres, a predefinição é ""|Dinâmica|Thumbprints dos certificados utilizados pelos clientes na função de administrador. É uma lista de nomes separados por vírgulas. |
|AADTokenEndpointFormat|cadeia de caracteres, a predefinição é ""|Estático|AAD ponto final do Token, Azure Commercial, do padrão especificado para o ambiente de não-padrão, como o Azure Government "https://login.microsoftonline.us/{0}" |
|AdminClientClaims|cadeia de caracteres, a predefinição é ""|Dinâmica|Todos os possíveis declarações esperadas de clientes da administração; o mesmo formato que ClientClaims; Esta lista internamente é adicionada à ClientClaims; portanto, não é necessário também adicionar as entradas do mesmo a ClientClaims. |
|AdminClientIdentities|cadeia de caracteres, a predefinição é ""|Dinâmica|Identidades do Windows de clientes de recursos de infraestrutura na função de administrador utilizado para autorizar as operações de recursos de infraestrutura com privilégios. É uma lista separada por vírgulas; cada entrada é um nome de conta de domínio ou o nome do grupo. Para sua comodidade; a conta que executa fabric.exe é atribuída automaticamente a função de administrador portanto, é agrupar ServiceFabricAdministrators. |
|AppRunAsAccountGroupX509Folder|cadeia de caracteres, predefinido é /home/sfuser/sfusercerts |Estático|Pasta onde se encontram AppRunAsAccountGroup X509 certificados e chaves privadas |
|CertificateExpirySafetyMargin|Período de tempo, a predefinição é Common::TimeSpan::FromMinutes(43200)|Estático|Especifique o período de tempo em segundos. Margem de segurança para a expiração do certificado; Estado de relatório de estado de funcionamento do certificado é alterado de OK para aviso quando a expiração é mais parecido com que isso. A predefinição é 30 dias. |
|CertificateHealthReportingInterval|Período de tempo, a predefinição é Common::TimeSpan::FromSeconds(3600 * 8)|Estático|Especifique o período de tempo em segundos. Especifique o intervalo de relatórios de estado de funcionamento de certificado; predefinida para 8 horas; definição como 0 desativa a relatórios de estado de funcionamento do certificado |
|ClientCertThumbprints|cadeia de caracteres, a predefinição é ""|Dinâmica|Thumbprints dos certificados utilizados pelos clientes para comunicar com o cluster; utiliza-o cluster de autorizar a ligação de entrada. É uma lista de nomes separados por vírgulas. |
|ClientClaimAuthEnabled|bool, a predefinição é falso|Estático|Indica se a autenticação baseada em afirmações está ativada nos clientes; definir este implicitamente verdadeiro define ClientRoleEnabled. |
|ClientClaims|cadeia de caracteres, a predefinição é ""|Dinâmica|Todos os possíveis declarações esperadas de clientes para ligar ao gateway. Esta é uma lista de 'Ou': ClaimsEntry \| \| ClaimsEntry \| \| ClaimsEntry... cada ClaimsEntry é uma lista de "E": ClaimType = ClaimValue & & ClaimType = ClaimValue & & ClaimType = ClaimValue... |
|ClientIdentities|cadeia de caracteres, a predefinição é ""|Dinâmica|Identidades do Windows do FabricClient; nomenclatura de gateway utiliza-o para autorizar as ligações de entrada. É uma lista separada por vírgulas; cada entrada é um nome de conta de domínio ou o nome do grupo. Para sua comodidade; a conta que executa fabric.exe automaticamente é permitida. então, são grupo ServiceFabricAllowedUsers e ServiceFabricAdministrators. |
|ClientRoleEnabled|bool, a predefinição é falso|Estático|Indica se a função de cliente está ativada; Quando definido como true; os clientes são atribuídos a funções com base nas suas identidades. Para V2; Ativar isso significa que o cliente não está no AdminClientCommonNames/AdminClientIdentities só pode executar operações só de leitura. |
|ClusterCertThumbprints|cadeia de caracteres, a predefinição é ""|Dinâmica|Thumbprints de certificados podem ser associados ao cluster; uma lista de nomes separados por vírgulas. |
|ClusterCredentialType|cadeia de caracteres, predefinido é "None"|Não Permitido|Indica o tipo de credenciais de segurança à sua disposição para proteger o cluster. Os valores válidos são "Nenhum/X509/Windows" |
|ClusterIdentities|cadeia de caracteres, a predefinição é ""|Dinâmica|Identidades do Windows de nós de cluster; utilizado para autorização de associação do cluster. É uma lista separada por vírgulas; cada entrada é um nome de conta de domínio ou o nome do grupo |
|ClusterSpn|cadeia de caracteres, a predefinição é ""|Não Permitido|Nome principal de serviço do cluster; Quando os recursos de infraestrutura é executado como um utilizador de domínio único (conta de utilizador de domínio/gMSA). É o SPN de serviços de escuta de concessão e serviços de escuta no fabric.exe: serviços de escuta de Federação; Serviços de escuta de replicação interna; serviço de escuta de serviço de tempo de execução e o serviço de escuta de gateway nomenclatura. Isso deve ser deixado em branco quando recursos de infraestrutura é executado como contas de computador; ligar caso em que o serviço de escuta de computação de lado o SPN do serviço de escuta de transporte endereço. |
|CrlCheckingFlag|uint, a predefinição é 0x40000000|Dinâmica|Sinalizador de validação de cadeia de certificado predefinido; pode ser substituído pelo sinalizador de componente específicas; Por exemplo, Federação/X509CertChainFlags 0x10000000 CERT_CHAIN_REVOCATION_CHECK_END_CERT 0x20000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN 0x40000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT 0x80000000 CERT_CHAIN_REVOCATION_CHECK_CACHE_ DwFlags de CertGetCertificateChain é documentada definição apenas para 0 desativa CRL verificação lista completa de valores suportados: http://msdn.microsoft.com/library/windows/desktop/aa376078(v=vs.85).aspx |
|CrlDisablePeriod|Período de tempo, a predefinição é Common::TimeSpan::FromMinutes(15)|Dinâmica|Especifique o período de tempo em segundos. O tempo que a verificação CRL está desativada para um determinado certificado depois de encontrar o erro offline; Se o erro CRL offline pode ser ignorado. |
|CrlOfflineHealthReportTtl|Período de tempo, a predefinição é Common::TimeSpan::FromMinutes(1440)|Dinâmica|Especifique o período de tempo em segundos. |
|DisableFirewallRuleForDomainProfile| bool, a predefinição é TRUE |Estático| Indica se a regra de firewall não deve ser ativada para o perfil de domínio |
|DisableFirewallRuleForPrivateProfile| bool, a predefinição é TRUE |Estático| Indica se a regra de firewall não deve ser ativada para o perfil privado | 
|DisableFirewallRuleForPublicProfile| bool, a predefinição é TRUE | Estático|Indica se a regra de firewall não deve ser ativada para o perfil público |
|FabricHostSpn| cadeia de caracteres, a predefinição é "" |Estático| Nome principal de serviço do FabricHost; Quando os recursos de infraestrutura é executada como um utilizador de domínio único (conta de utilizador de domínio/gMSA) e FabricHost é executado na conta de computador. É o serviço de escuta de SPN do IPC para FabricHost; que, por padrão deve ser deixado em branco, uma vez que FabricHost é executado na conta de computador |
|IgnoreCrlOfflineError|bool, a predefinição é falso|Dinâmica|Se pretende ignorar o erro CRL offline quando o servidor verifica certificados de cliente recebidos |
|IgnoreSvrCrlOfflineError|bool, a predefinição é TRUE|Dinâmica|Se pretende ignorar o erro CRL offline quando do lado do cliente verifica a entrada certificados de servidor; predefinido como true. Necessitam de ataques com certificados de servidor revogados comprometer DNS; mais difícil do que com certificados de cliente revogados. |
|ServerAuthCredentialType|cadeia de caracteres, predefinido é "None"|Estático|Indica o tipo de credenciais de segurança à sua disposição para proteger a comunicação entre FabricClient e o Cluster. Os valores válidos são "Nenhum/X509/Windows" |
|ServerCertThumbprints|cadeia de caracteres, a predefinição é ""|Dinâmica|Thumbprints dos certificados de servidor utilizados pelo cluster para comunicar com clientes; os clientes utilizam esta para autenticar o cluster. É uma lista de nomes separados por vírgulas. |
|SettingsX509StoreName| cadeia de caracteres, predefinido é "MY"| Dinâmica|Arquivo utilizado por recursos de infraestrutura para proteção da configuração de certificado X509 |
|UseClusterCertForIpcServerTlsSecurity|bool, a predefinição é falso|Estático|Se pretende utilizar o certificado de cluster para proteger o IPC Server TLS a unidade de transporte |
|X509Folder|cadeia de caracteres, predefinido é /var/lib/waagent|Estático|Pasta onde X509 certificados e chaves privadas estão localizadas |

## <a name="securityadminclientx509names"></a>Security/AdminClientX509Names
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou descrição breve** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, a predefinição é nenhum|Dinâmica|Esta é uma lista de par "Name" e "Valor". Cada "Name" é do nome comum da entidade ou DnsName do X509 certificados está autorizados para operações de cliente de administrador. Para um determinado "nome", "Value" é uma lista separada por vírgulas de thumbprints de certificado de emissor afixação, se não vazio, o emissor direto de certificados de cliente de administrador tem de estar na lista. |

## <a name="securityclientaccess"></a>Segurança/ClientAccess
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou descrição breve** |
| --- | --- | --- | --- |
|ActivateNode |cadeia de caracteres, predefinido for "Admin" |Dinâmica| Configuração de segurança para a ativação de um nó. |
|CancelTestCommand |cadeia de caracteres, predefinido for "Admin" |Dinâmica| Cancela uma TestCommand específica - se estiver em trânsito. |
|CodePackageControl |cadeia de caracteres, predefinido for "Admin" |Dinâmica| Configuração de segurança para o reinício de pacotes do código. |
|CreateApplication |cadeia de caracteres, predefinido for "Admin" | Dinâmica|Configuração de segurança para a criação do aplicativo. |
|CreateComposeDeployment|cadeia de caracteres, predefinido for "Admin"| Dinâmica|Cria uma implementação de composição descrita por ficheiros de composição |
|CreateGatewayResource|cadeia de caracteres, predefinido for "Admin"| Dinâmica|Criar um recurso de gateway |
|CreateName |cadeia de caracteres, predefinido for "Admin" |Dinâmica|Configuração de segurança para a criação do URI de nomenclatura. |
|CreateNetwork|cadeia de caracteres, predefinido for "Admin" |Dinâmica|Cria uma rede de contentor |
|CreateService |cadeia de caracteres, predefinido for "Admin" |Dinâmica| Configuração de segurança para a criação do serviço. |
|CreateServiceFromTemplate |cadeia de caracteres, predefinido for "Admin" |Dinâmica|Configuração de segurança para a criação do serviço do modelo. |
|CreateVolume|cadeia de caracteres, predefinido for "Admin"|Dinâmica|Cria um volume |
|DeactivateNode |cadeia de caracteres, predefinido for "Admin" |Dinâmica| Configuração de segurança para desativar um nó. |
|DeactivateNodesBatch |cadeia de caracteres, predefinido for "Admin" |Dinâmica| Configuração de segurança para o desativar vários nós. |
|Eliminar |cadeia de caracteres, predefinido for "Admin" |Dinâmica| Configurações de segurança para a imagem de armazenam a operação de eliminação de cliente. |
|DeleteApplication |cadeia de caracteres, predefinido for "Admin" |Dinâmica| Configuração de segurança para a eliminação de aplicação. |
|DeleteComposeDeployment|cadeia de caracteres, predefinido for "Admin"| Dinâmica|Elimina a implementação de composição |
|DeleteGatewayResource|cadeia de caracteres, predefinido for "Admin"| Dinâmica|Elimina um recurso de gateway |
|DeleteName |cadeia de caracteres, predefinido for "Admin" |Dinâmica|Configuração de segurança para a eliminação de URI de nomenclatura. |
|DeleteNetwork|cadeia de caracteres, predefinido for "Admin" |Dinâmica|Elimina uma rede de contentor |
|DeleteService |cadeia de caracteres, predefinido for "Admin" |Dinâmica|Configuração de segurança para a eliminação de serviço. |
|DeleteVolume|cadeia de caracteres, predefinido for "Admin"|Dinâmica|Elimina um volume.| 
|EnumerateProperties |cadeia de caracteres, a predefinição é "administrador\|\|utilizador" | Dinâmica|Configuração de segurança para atribuir nomes a enumeração de propriedades. |
|EnumerateSubnames |cadeia de caracteres, a predefinição é "administrador\|\|utilizador" |Dinâmica| Configuração de segurança para a enumeração de URI de nomenclatura. |
|FileContent |cadeia de caracteres, predefinido for "Admin" |Dinâmica| Transferência de ficheiros de cliente (externo ao cluster) do arquivo de configuração de segurança para a imagem. |
|FileDownload |cadeia de caracteres, predefinido for "Admin" |Dinâmica| Configuração de segurança para imagem store cliente ficheiro download início (externo ao cluster). |
|FinishInfrastructureTask |cadeia de caracteres, predefinido for "Admin" |Dinâmica| Configuração de segurança para terminar tarefas de infraestrutura. |
|GetChaosReport | cadeia de caracteres, a predefinição é "administrador\|\|utilizador" |Dinâmica| Obtém o estado caótico dentro de um intervalo de tempo especificado. |
|GetClusterConfiguration | cadeia de caracteres, a predefinição é "administrador\|\|utilizador" | Dinâmica|Induces GetClusterConfiguration numa partição. |
|GetClusterConfigurationUpgradeStatus | cadeia de caracteres, a predefinição é "administrador\|\|utilizador" |Dinâmica| Induces GetClusterConfigurationUpgradeStatus numa partição. |
|GetFabricUpgradeStatus |cadeia de caracteres, a predefinição é "administrador\|\|utilizador" |Dinâmica| Configuração de segurança para consultar o estado de atualização do cluster. |
|GetNodeDeactivationStatus |cadeia de caracteres, predefinido for "Admin" |Dinâmica| Configuração de segurança para a verificar o estado de Desativação. |
|GetNodeTransitionProgress | cadeia de caracteres, a predefinição é "administrador\|\|utilizador" |Dinâmica| Configuração de segurança para obter o progresso de um comando de transição de nó. |
|GetPartitionDataLossProgress | cadeia de caracteres, a predefinição é "administrador\|\|utilizador" | Dinâmica|Obtém o progresso de uma chamada de api de perda de dados de invoke. |
|GetPartitionQuorumLossProgress | cadeia de caracteres, a predefinição é "administrador\|\|utilizador" |Dinâmica| Obtém o progresso de uma chamada de api de perda de quórum de invoke. |
|GetPartitionRestartProgress | cadeia de caracteres, a predefinição é "administrador\|\|utilizador" |Dinâmica| Obtém o progresso de uma chamada de api de partição de reinício. |
|GetSecrets|cadeia de caracteres, predefinido for "Admin"|Dinâmica|Obter valores secretos |
|GetServiceDescription |cadeia de caracteres, a predefinição é "administrador\|\|utilizador" |Dinâmica| Configuração de segurança para notificações do serviço de pesquisa de longa e ler as descrições de serviço. |
|GetStagingLocation |cadeia de caracteres, predefinido for "Admin" |Dinâmica| Cliente de obtenção de localização de transição do arquivo de configuração de segurança para a imagem. |
|GetStoreLocation |cadeia de caracteres, predefinido for "Admin" |Dinâmica| Obtenção de localização de armazenamento do cliente do arquivo de configuração de segurança para a imagem. |
|GetUpgradeOrchestrationServiceState|cadeia de caracteres, predefinido for "Admin"| Dinâmica|Induces GetUpgradeOrchestrationServiceState numa partição |
|GetUpgradesPendingApproval |cadeia de caracteres, predefinido for "Admin" |Dinâmica| Induces GetUpgradesPendingApproval numa partição. |
|GetUpgradeStatus |cadeia de caracteres, a predefinição é "administrador\|\|utilizador" |Dinâmica| Configuração de segurança para consultar o estado de atualização de aplicação. |
|InternalList |cadeia de caracteres, predefinido for "Admin" | Dinâmica|Operação de lista de ficheiros de cliente (interna) do arquivo de configuração de segurança para a imagem. |
|InvokeContainerApi|cadeia de caracteres, predefinido for "Admin"|Dinâmica|Invocar a API de contentor |
|InvokeInfrastructureCommand |cadeia de caracteres, predefinido for "Admin" |Dinâmica| Configuração de segurança para comandos de gestão de tarefas de infraestrutura. |
|InvokeInfrastructureQuery |cadeia de caracteres, a predefinição é "administrador\|\|utilizador" | Dinâmica|Configuração de segurança para consultar tarefas de infraestrutura. |
|Lista |cadeia de caracteres, a predefinição é "administrador\|\|utilizador" | Dinâmica|Operação de lista de ficheiros de cliente do arquivo de configuração de segurança para a imagem. |
|MoveNextFabricUpgradeDomain |cadeia de caracteres, predefinido for "Admin" |Dinâmica| Configuração de segurança para retomar as atualizações de cluster com um domínio de atualização de explicity. |
|MoveNextUpgradeDomain |cadeia de caracteres, predefinido for "Admin" |Dinâmica| Configuração de segurança para retomar as atualizações de aplicações com um domínio de atualização de mensagens em fila explícita. |
|MoveReplicaControl |cadeia de caracteres, predefinido for "Admin" | Dinâmica|Mova a réplica. |
|NameExists |cadeia de caracteres, a predefinição é "administrador\|\|utilizador" | Dinâmica|Configuração de segurança para as verificações de existência do URI de nomenclatura. |
|NodeControl |cadeia de caracteres, predefinido for "Admin" |Dinâmica| Configuração de segurança para iniciar; a parar; e nós a reiniciar. |
|NodeStateRemoved |cadeia de caracteres, predefinido for "Admin" |Dinâmica| Configuração de segurança para relatórios de estado do nó removido. |
|Ping |cadeia de caracteres, a predefinição é "administrador\|\|utilizador" |Dinâmica| Configuração de segurança para os pings do cliente. |
|PredeployPackageToNode |cadeia de caracteres, predefinido for "Admin" |Dinâmica| Api de pré-implantação. |
|PrefixResolveService |cadeia de caracteres, a predefinição é "administrador\|\|utilizador" |Dinâmica| Configuração de segurança para a resolução de prefixo de serviço com base em conformidade. |
|PropertyReadBatch |cadeia de caracteres, a predefinição é "administrador\|\|utilizador" |Dinâmica| Configuração de segurança para a propriedade de nomenclatura de operações de leitura. |
|PropertyWriteBatch |cadeia de caracteres, predefinido for "Admin" |Dinâmica|Configurações de segurança para a propriedade de nomenclatura de operações de escrita. |
|ProvisionApplicationType |cadeia de caracteres, predefinido for "Admin" |Dinâmica| Configuração de segurança para o aprovisionamento de tipo de aplicação. |
|ProvisionFabric |cadeia de caracteres, predefinido for "Admin" |Dinâmica| Configuração de segurança para o aprovisionamento manifesto MSI e/ou de Cluster. |
|Consulta |cadeia de caracteres, a predefinição é "administrador\|\|utilizador" |Dinâmica| Configuração de segurança para consultas. |
|RecoverPartition |cadeia de caracteres, predefinido for "Admin" | Dinâmica|Configuração de segurança para recuperar uma partição. |
|RecoverPartitions |cadeia de caracteres, predefinido for "Admin" | Dinâmica|Configuração de segurança para recuperar partições. |
|RecoverServicePartitions |cadeia de caracteres, predefinido for "Admin" |Dinâmica| Configuração de segurança para recuperar as partições do serviço. |
|RecoverSystemPartitions |cadeia de caracteres, predefinido for "Admin" |Dinâmica| Configuração de segurança para recuperar as partições do serviço de sistema. |
|RemoveNodeDeactivations |cadeia de caracteres, predefinido for "Admin" |Dinâmica| Configuração de segurança para a reverter desativação em vários nós. |
|ReportFabricUpgradeHealth |cadeia de caracteres, predefinido for "Admin" |Dinâmica| Configuração de segurança para retomar as atualizações de cluster com o progresso da atualização atual. |
|ReportFault |cadeia de caracteres, predefinido for "Admin" |Dinâmica| Configuração de segurança para relatórios de falhas. |
|ReportHealth |cadeia de caracteres, predefinido for "Admin" |Dinâmica| Configuração de segurança para relatórios de estado de funcionamento. |
|ReportUpgradeHealth |cadeia de caracteres, predefinido for "Admin" |Dinâmica| Configuração de segurança para retomar as atualizações de aplicações com o progresso da atualização atual. |
|ResetPartitionLoad |cadeia de caracteres, a predefinição é "administrador\|\|utilizador" |Dinâmica| Configuração de segurança para reposição de carga para um failoverUnit. |
|ResolveNameOwner |cadeia de caracteres, a predefinição é "administrador\|\|utilizador" | Dinâmica|Configuração de segurança para a resolução de proprietário de URI de nomenclatura. |
|ResolvePartition |cadeia de caracteres, a predefinição é "administrador\|\|utilizador" | Dinâmica|Configuração de segurança para a resolução de serviços do sistema. |
|ResolveService |cadeia de caracteres, a predefinição é "administrador\|\|utilizador" |Dinâmica| Configuração de segurança para a resolução de serviço com base em conformidade. |
|ResolveSystemService|cadeia de caracteres, a predefinição é "administrador\|\|utilizador"|Dinâmica| Configuração de segurança para a resolução de serviços do sistema |
|RollbackApplicationUpgrade |cadeia de caracteres, predefinido for "Admin" |Dinâmica| Configuração de segurança para reverter as atualizações de aplicações. |
|RollbackFabricUpgrade |cadeia de caracteres, predefinido for "Admin" |Dinâmica| Configuração de segurança para reverter as atualizações de cluster. |
|ServiceNotifications |cadeia de caracteres, a predefinição é "administrador\|\|utilizador" |Dinâmica| Configuração de segurança para notificações de serviço baseado em evento. |
|SetUpgradeOrchestrationServiceState|cadeia de caracteres, predefinido for "Admin"| Dinâmica|Induces SetUpgradeOrchestrationServiceState numa partição |
|StartApprovedUpgrades |cadeia de caracteres, predefinido for "Admin" |Dinâmica| Induces StartApprovedUpgrades numa partição. |
|StartChaos |cadeia de caracteres, predefinido for "Admin" |Dinâmica| Inicia o Chaos - se não ainda tiver iniciado. |
|StartClusterConfigurationUpgrade |cadeia de caracteres, predefinido for "Admin" |Dinâmica| Induces StartClusterConfigurationUpgrade numa partição. |
|StartInfrastructureTask |cadeia de caracteres, predefinido for "Admin" | Dinâmica|Configuração de segurança para iniciar tarefas de infraestrutura. |
|StartNodeTransition |cadeia de caracteres, predefinido for "Admin" |Dinâmica| Configuração de segurança para iniciar uma transição de nó. |
|StartPartitionDataLoss |cadeia de caracteres, predefinido for "Admin" |Dinâmica| Induces perda de dados numa partição. |
|StartPartitionQuorumLoss |cadeia de caracteres, predefinido for "Admin" |Dinâmica| Induces perda de quórum numa partição. |
|StartPartitionRestart |cadeia de caracteres, predefinido for "Admin" |Dinâmica| Reinicia simultaneamente algumas ou todas as réplicas de uma partição. |
|StopChaos |cadeia de caracteres, predefinido for "Admin" |Dinâmica| Para o Chaos - se este foi iniciado. |
|ToggleVerboseServicePlacementHealthReporting | cadeia de caracteres, a predefinição é "administrador\|\|utilizador" |Dinâmica| Configuração de segurança para alternar HealthReporting de ServicePlacement verboso. |
|UnprovisionApplicationType |cadeia de caracteres, predefinido for "Admin" |Dinâmica| Configuração de segurança para anulação do aprovisionamento tipo de aplicação. |
|UnprovisionFabric |cadeia de caracteres, predefinido for "Admin" |Dinâmica| Configuração de segurança para o Cluster / MSI manifesto anulação do aprovisionamento. |
|UnreliableTransportControl |cadeia de caracteres, predefinido for "Admin" |Dinâmica| Transporte não fiável para adicionar e remover comportamentos. |
|UpdateService |cadeia de caracteres, predefinido for "Admin" |Dinâmica|Configuração de segurança para atualizações de serviço. |
|UpgradeApplication |cadeia de caracteres, predefinido for "Admin" |Dinâmica| Configuração de segurança para iniciar ou interromper as atualizações de aplicações. |
|UpgradeComposeDeployment|cadeia de caracteres, predefinido for "Admin"| Dinâmica|Atualiza a implementação de composição |
|UpgradeFabric |cadeia de caracteres, predefinido for "Admin" |Dinâmica| Configuração de segurança para iniciar as atualizações de cluster. |
|Carregar |cadeia de caracteres, predefinido for "Admin" | Dinâmica|A operação de carregamento do cliente do arquivo de configuração de segurança para a imagem. |

## <a name="securityclientcertificateissuerstores"></a>Security/ClientCertificateIssuerStores
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou descrição breve** |
| --- | --- | --- | --- |
|PropertyGroup|IssuerStoreKeyValueMap, a predefinição é nenhum |Dinâmica|X509 arquivos de certificados de emissor de certificados de cliente Nome = clientIssuerCN; Valor = lista separada por vírgulas de lojas |

## <a name="securityclientx509names"></a>Security/ClientX509Names
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou descrição breve** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, a predefinição é nenhum|Dinâmica|Esta é uma lista de par "Name" e "Valor". Cada "Name" é do nome comum da entidade ou DnsName do X509 certificados está autorizados para operações de cliente. Para um determinado "nome", "Value" é uma lista separada por vírgulas de thumbprints de certificado de emissor afixação, se não vazio, o emissor direto de certificados de cliente tem de estar na lista.|

## <a name="securityclustercertificateissuerstores"></a>Security/ClusterCertificateIssuerStores
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou descrição breve** |
| --- | --- | --- | --- |
|PropertyGroup|IssuerStoreKeyValueMap, a predefinição é nenhum |Dinâmica|X509 issuer certificate stores for cluster certificates; Name = clusterIssuerCN; Value = comma separated list of stores |

## <a name="securityclusterx509names"></a>Security/ClusterX509Names
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou descrição breve** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, a predefinição é nenhum|Dinâmica|Esta é uma lista de par "Name" e "Valor". Cada "Name" é do nome comum da entidade ou DnsName do X509 certificados está autorizados para operações de cluster. Para um determinado "nome", "Value" é uma lista separada por vírgulas de thumbprints de certificado de emissor afixação, se não vazio, o emissor direto de certificados de cluster tem de estar na lista.|

## <a name="securityservercertificateissuerstores"></a>Security/ServerCertificateIssuerStores
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou descrição breve** |
| --- | --- | --- | --- |
|PropertyGroup|IssuerStoreKeyValueMap, a predefinição é nenhum |Dinâmica|X509 issuer certificate stores for server certificates; Name = serverIssuerCN; Value = comma separated list of stores |

## <a name="securityserverx509names"></a>Security/ServerX509Names
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou descrição breve** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, a predefinição é nenhum|Dinâmica|Esta é uma lista de par "Name" e "Valor". Cada "Name" é do nome comum da entidade ou DnsName do X509 certificados está autorizados para operações de servidor. Para um determinado "nome", "Value" é uma lista separada por vírgulas de thumbprints de certificado de emissor afixação, se não vazio, o emissor direto de certificados de servidor tem de estar na lista.|

## <a name="setup"></a>Configurar
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou descrição breve** |
| --- | --- | --- | --- |
|ContainerNetworkName|cadeia de caracteres, a predefinição é ""| Estático |O nome de rede a utilizar quando configurar uma rede de contentor.|
|ContainerNetworkSetup|bool, a predefinição é falso| Estático |Se configurar uma rede de contentor.|
|FabricDataRoot |String | Não Permitido |Diretório de raiz de dados do Service Fabric. Predefinido para o Azure está d:\svcfab |
|FabricLogRoot |String | Não Permitido |Diretório de raiz do registo de recursos de infraestrutura do serviço. Trata-se em que são colocados os registos de SF e rastreios. |
|NodesToBeRemoved|cadeia de caracteres, a predefinição é ""| Dinâmica |Os nós que devem ser removidos como parte da atualização de configuração. (Apenas para implementações de autónomo)|
|ServiceRunAsAccountName |String | Não Permitido |O nome da conta sob a qual pretende executar o serviço de anfitrião de recursos de infraestrutura. |
|SkipContainerNetworkResetOnReboot|bool, a predefinição é falso|NotAllowed|Se ignorar a repor rede de contentor na reinicialização.|
|SkipFirewallConfiguration |Bool, a predefinição é falso | Não Permitido |Especifica se as definições da firewall tem de ser definido pelo sistema ou não. Isto aplica-se apenas se estiver a utilizar o firewall do windows. Se estiver a utilizar firewalls de terceiros, em seguida, tem de abrir as portas para o sistema e as aplicações para utilizar |

## <a name="tokenvalidationservice"></a>TokenValidationService
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou descrição breve** |
| --- | --- | --- | --- |
|Fornecedores |cadeia de caracteres, predefinido é "DSTS" |Estático|Lista separada por vírgulas dos fornecedores de validação do token para ativar (provedores válidos são: DSTS; AAD). Atualmente apenas um único fornecedor pode ser ativado em qualquer altura. |

## <a name="traceetw"></a>Rastreio/Etw
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou descrição breve** |
| --- | --- | --- | --- |
|Nível |Int, a predefinição é 4 | Dinâmica |Nível de rastreio de etw pode assumir valores 1, 2, 3, 4. Suporte é necessário manter o nível de rastreio em 4 |

## <a name="transactionalreplicator"></a>TransactionalReplicator
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou descrição breve** |
| --- | --- | --- | --- |
|BatchAcknowledgementInterval | Tempo em segundos, a predefinição é 0.015 | Estático | Especifique o período de tempo em segundos. Determina a quantidade de tempo que o replicator aguarda a após o recebimento de uma operação antes de enviar uma confirmação de volta. Outras operações recebidas durante este período de tempo terão suas confirmações enviadas novamente numa única mensagem -> reduzindo o tráfego de rede, mas potencialmente reduzindo a produtividade do replicador. |
|MaxCopyQueueSize |Uint, a predefinição é 16384 | Estático |Este é o máximo valor define o tamanho inicial para a fila que mantém as operações de replicação. Tenha em atenção que tem de ser uma potência de 2. Se durante o tempo de execução, a fila aumenta para esta operação de tamanho ficará limitada entre os replicators primários e secundários. |
|MaxPrimaryReplicationQueueMemorySize |Uint, o padrão é 0 | Estático |Este é o valor máximo da fila de replicação primária em bytes. |
|MaxPrimaryReplicationQueueSize |Uint, a predefinição é 8192 | Estático |Este é o número máximo de operações que pode existir na fila de replicação primária. Tenha em atenção que tem de ser uma potência de 2. |
|MaxReplicationMessageSize |uint, a predefinição é 52428800 | Estático | Tamanho da mensagem máximo de operações de replicação. A predefinição é 50MB. |
|MaxSecondaryReplicationQueueMemorySize |Uint, o padrão é 0 | Estático |Este é o valor máximo da fila de replicação secundário em bytes. |
|MaxSecondaryReplicationQueueSize |Uint, a predefinição é 16384 | Estático |Este é o número máximo de operações que pode existir na fila de replicação secundário. Tenha em atenção que tem de ser uma potência de 2. |
|ReplicatorAddress |cadeia de caracteres, predefinido é "localhost:0" | Estático | O ponto final na forma de uma cadeia de caracteres - o "IP: porta" que é utilizada pelo replicador de recursos de infraestrutura do Windows para estabelecer ligações com outras réplicas para operações de envio/receção. |

## <a name="transport"></a>Transporte
| **Parâmetro** | **Valores permitidos** |**Política de atualização** |**Documentação de orientação ou descrição breve** |
| --- | --- | --- | --- |
|ConnectionOpenTimeout|Período de tempo, a predefinição é Common::TimeSpan::FromSeconds(60)|Estático|Especifique o período de tempo em segundos. Tempo limite para a configuração de ligação no lado de entrada e de aceitar (incluindo a negociação de segurança em modo seguro) |
|FrameHeaderErrorCheckingEnabled|bool, a predefinição é TRUE|Estático|Predefinição para a verificação no cabeçalho de quadro no modo de não segura; de erros definição de componente substitui isso. |
|MessageErrorCheckingEnabled|bool, a predefinição é falso|Estático|Predefinição para a verificação no cabeçalho da mensagem e o corpo no modo de não segura; de erros definição de componente substitui isso. |
|ResolveOption|cadeia de caracteres, a predefinição é "não especificada"|Estático|Determina a forma como o FQDN for resolvido.  Valores válidos são "unspecified/ipv4/ipv6". |
|SendTimeout|Período de tempo, a predefinição é Common::TimeSpan::FromSeconds(300)|Dinâmica|Especifique o período de tempo em segundos. Tempo limite para a detecção de ligação paralisada de envio. Relatórios de falhas TCP não são confiáveis num ambiente. Isto poderá ter de ser ajustado, de acordo com largura de banda disponível e o tamanho dos dados de saída (\*MaxMessageSize\/\*SendQueueSizeLimit). |

## <a name="upgradeorchestrationservice"></a>UpgradeOrchestrationService
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou descrição breve** |
| --- | --- | --- | --- |
|AutoupgradeEnabled | Bool, a predefinição é verdadeiro |Estático| Consulta automática e a ação de atualização com base num arquivo de estado de objetivos. |
|AutoupgradeInstallEnabled|bool, a predefinição é falso|Estático|Consulta automática, o aprovisionamento e a instalação de código atualizam ações com base num arquivo de estado de objetivos.|
|GoalStateExpirationReminderInDays|int, a predefinição é 30|Estático|Define o número de dias restantes após o qual o lembrete de estado do objetivo deve ser mostrada.|
|MinReplicaSetSize |int, a predefinição é 0 |Estático |O MinReplicaSetSize para UpgradeOrchestrationService.|
|PlacementConstraints | cadeia de caracteres, a predefinição é "" |Estático| O PlacementConstraints para UpgradeOrchestrationService. |
|QuorumLossWaitDuration | Tempo em segundos, a predefinição é MaxValue |Estático| Especifique o período de tempo em segundos. O QuorumLossWaitDuration para UpgradeOrchestrationService. |
|ReplicaRestartWaitDuration | Tempo em segundos, a predefinição é 60 minutos|Estático| Especifique o período de tempo em segundos. O ReplicaRestartWaitDuration para UpgradeOrchestrationService. |
|StandByReplicaKeepDuration | Tempo em segundos, a predefinição é 60*24*7 minutos |Estático| Especifique o período de tempo em segundos. O StandByReplicaKeepDuration para UpgradeOrchestrationService. |
|TargetReplicaSetSize |int, a predefinição é 0 |Estático |O TargetReplicaSetSize para UpgradeOrchestrationService. |
|UpgradeApprovalRequired | Bool, a predefinição é falso | Estático|Definir como para fazer a atualização do código exigir a aprovação de administrador antes de continuar. |

## <a name="upgradeservice"></a>UpgradeService
| **Parâmetro** | **Valores permitidos** | **Política de atualização** | **Documentação de orientação ou descrição breve** |
| --- | --- | --- | --- |
|BaseUrl | cadeia de caracteres, a predefinição é "" |Estático|BaseUrl para UpgradeService. |
|ClusterId | cadeia de caracteres, a predefinição é "" |Estático|ClusterId para UpgradeService. |
|CoordinatorType | cadeia de caracteres, predefinido é "WUTest"|Não Permitido|O CoordinatorType para UpgradeService. |
|MinReplicaSetSize | Int, a predefinição é 2 |Não Permitido| O MinReplicaSetSize para UpgradeService. |
|OnlyBaseUpgrade | Bool, a predefinição é falso |Dinâmica|OnlyBaseUpgrade para UpgradeService. |
|PlacementConstraints |cadeia de caracteres, a predefinição é "" |Não Permitido|O PlacementConstraints para o serviço de atualização. |
|PollIntervalInSeconds|Período de tempo, a predefinição é Common::TimeSpan::FromSeconds(60) |Dinâmica|Especifique o período de tempo em segundos. O intervalo entre a consulta de UpgradeService para operações de gestão do ARM. |
|TargetReplicaSetSize | Int, a predefinição é 3 |Não Permitido| O TargetReplicaSetSize para UpgradeService. |
|TestCabFolder | cadeia de caracteres, a predefinição é "" |Estático| TestCabFolder para UpgradeService. |
|X509FindType | cadeia de caracteres, a predefinição é ""|Dinâmica| X509FindType para UpgradeService. |
|X509FindValue | cadeia de caracteres, a predefinição é "" |Dinâmica| X509FindValue para UpgradeService. |
|X509SecondaryFindValue | cadeia de caracteres, a predefinição é "" |Dinâmica| X509SecondaryFindValue para UpgradeService. |
|X509StoreLocation | cadeia de caracteres, a predefinição é "" |Dinâmica| X509StoreLocation para UpgradeService. |
|X509StoreName | cadeia de caracteres, a predefinição é "Meu"|Dinâmica|X509StoreName para UpgradeService. |

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações, consulte [atualizar a configuração de um cluster do Azure](service-fabric-cluster-config-upgrade-azure.md) e [atualizar a configuração de um cluster autónomo](service-fabric-cluster-config-upgrade-windows-server.md).
