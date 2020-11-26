---
title: Utilização de contas de armazenamento geridas pelo cliente no Azure Monitor Log Analytics
description: Use a sua própria conta de armazenamento para cenários de Log Analytics
ms.subservice: logs
ms.topic: conceptual
author: noakup
ms.author: noakuper
ms.date: 09/03/2020
ms.openlocfilehash: f424a2c3102f7b270a64c612a91d645ab71461fc
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184098"
---
# <a name="using-customer-managed-storage-accounts-in-azure-monitor-log-analytics"></a>Utilização de contas de armazenamento geridas pelo cliente no Azure Monitor Log Analytics

O Log Analytics conta com o Azure Storage em vários cenários. Esta utilização é normalmente gerida automaticamente. No entanto, alguns casos exigem que você forneça e gere a sua própria conta de armazenamento, também referida como uma conta de armazenamento gerida pelo cliente. Este documento detalha o uso do armazenamento gerido pelo cliente para a ingestão de registos WAD/LAD, cenários específicos de Ligação Privada e encriptação da chave gerida pelo cliente (CMK). 

> [!NOTE]
> Recomendamos que não assuma uma dependência dos conteúdos que o Log Analytics carrega para armazenamento gerido pelo cliente, dado que a formatação e o conteúdo podem mudar.

## <a name="ingesting-azure-diagnostics-extension-logs-wadlad"></a>Ingerir registos de extensão de Diagnóstico Azure (WAD/LAD)
Os agentes de extensão Azure Diagnostics (também chamados WAD e LAD para agentes Windows e Linux, respectivamente) recolhem vários registos do sistema operativo e armazenam-nos numa conta de armazenamento gerida pelo cliente. Em seguida, pode ingerir estes registos no Log Analytics para analisá-los e analisá-los.
Como recolher registos de extensão Azure Diagnostics da sua conta de armazenamento Conecte a conta de armazenamento ao seu espaço de trabalho Log Analytics como fonte de dados de armazenamento utilizando [o portal Azure](./diagnostics-extension-logs.md#collect-logs-from-azure-storage) ou ligando para a [API de Insights de Armazenamento](/rest/api/loganalytics/connectedsources/storage%20insights/createorupdate).

Tipos de dados suportados:
* Syslog
* Eventos do Windows
* Service Fabric
* Eventos DA ETW
* Registos do IIS

## <a name="using-private-links"></a>Utilização de links privados
As contas de armazenamento geridas pelo cliente são necessárias em alguns casos de utilização, quando ligações privadas são usadas para ligar aos recursos do Azure Monitor. Um desses casos é a ingestão de registos personalizados ou registos IIS. Estes tipos de dados são primeiro enviados como bolhas para uma conta de armazenamento Azure intermediário e só depois ingeridos num espaço de trabalho. Da mesma forma, algumas soluções do Azure Monitor podem usar contas de armazenamento para armazenar ficheiros grandes, como ficheiros de despejo watson, que são usados pela solução Azure Security Center. 

##### <a name="private-link-scenarios-that-require-a-customer-managed-storage"></a>Cenários de Ligação Privada que requerem um armazenamento gerido pelo cliente
* Ingestão de registos personalizados e registos IIS
* Permitindo que a solução ASC recolha ficheiros de despejo watson

### <a name="how-to-use-a-customer-managed-storage-account-over-a-private-link"></a>Como utilizar uma conta de armazenamento gerida pelo cliente sobre um Link Privado
##### <a name="workspace-requirements"></a>Requisitos do espaço de trabalho
Ao ligar-se ao Azure Monitor através de uma ligação privada, os agentes do Log Analytics só são capazes de enviar registos para espaços de trabalho ligados à sua rede através de um link privado. Esta regra requer que configuure corretamente um objeto Azure Monitor Private Link Scope (AMPLS), conecte-o aos seus espaços de trabalho e, em seguida, conecte o AMPLS à sua rede por uma ligação privada. Para obter mais informações sobre o procedimento de configuração AMPLS, consulte [utilizar o Link Privado Azure para ligar de forma segura as redes ao Azure Monitor](./private-link-security.md). 
##### <a name="storage-account-requirements"></a>Requisitos da conta de armazenamento
Para que a conta de armazenamento se conecte com sucesso ao seu link privado, deve:
* Esteja localizado no seu VNet ou numa rede espreitada e ligado ao seu VNet por um link privado. Isto permite que os agentes do seu VNet enviem registos para a conta de armazenamento.
* Localiza-se na mesma região que o espaço de trabalho a que está ligado.
* Permitir ao Monitor Azure aceder à conta de armazenamento. Se optou por permitir apenas que as redes selecionadas acedam à sua conta de armazenamento, também deverá permitir esta exceção: "permitir que serviços confiáveis da Microsoft acedam a esta conta de armazenamento". Isto permite que o Log Analytics leia os registos ingeridos nesta conta de armazenamento.
* Se o seu espaço de trabalho também lidar com o tráfego de outras redes, deverá configurar a conta de armazenamento para permitir a entrada de tráfego proveniente das redes/internet relevantes.

##### <a name="link-your-storage-account-to-a-log-analytics-workspace"></a>Ligue a sua conta de armazenamento a um espaço de trabalho Log Analytics
Pode ligar a sua conta de armazenamento ao espaço de trabalho através do [Azure CLI](/cli/azure/monitor/log-analytics/workspace/linked-storage) ou [REST API](/rest/api/loganalytics/linkedstorageaccounts). Valores de Dados AplicávelSourceType:
* CustomLogs – para utilizar o armazenamento para registos personalizados e registos IIS durante a ingestão.
* AzureWatson – use o armazenamento para ficheiros de despejo watson carregados pela solução ASC (Azure Security Center). Para obter mais informações sobre a gestão da retenção, substituição de uma conta de armazenamento ligada e monitorização da sua atividade de conta de armazenamento, consulte [Gerir contas de armazenamento ligadas.](#managing-linked-storage-accounts) 

## <a name="encrypting-data-with-cmk"></a>Encriptação de dados com CMK
O Azure Storage encripta todos os dados em repouso numa conta de armazenamento. Por predefinição, encripta dados com teclas geridas pela Microsoft (MMK). No entanto, o Azure Storage permitirá, em vez disso, utilizar uma chave gerida pelo Cliente (CMK) a partir do cofre da Chave Azure para encriptar os seus dados de armazenamento. Pode importar as suas próprias chaves para o Cofre da Chave Azure, ou pode usar as APIs do Cofre de Chaves Azure para gerar chaves.
##### <a name="cmk-scenarios-that-require-a-customer-managed-storage-account"></a>Cenários CMK que requerem uma conta de armazenamento gerida pelo cliente
* Encriptação de consultas de alerta de registo com CMK
* Encriptação de consultas guardadas com CMK

### <a name="how-to-apply-cmk-to-customer-managed-storage-accounts"></a>Como aplicar CMK a contas de armazenamento geridas pelo cliente
##### <a name="storage-account-requirements"></a>Requisitos da conta de armazenamento
A conta de armazenamento e o cofre-chave devem estar na mesma região, mas podem estar em diferentes subscrições. Para obter mais informações sobre encriptação do Azure Storage e gestão de chaves, consulte [a encriptação do Armazenamento Azure para obter dados em repouso](../../storage/common/storage-service-encryption.md).

##### <a name="apply-cmk-to-your-storage-accounts"></a>Aplicar CMK nas suas contas de armazenamento
Para configurar a sua conta de Armazenamento Azure para utilizar chaves geridas pelo cliente com cofre de chaves Azure, utilize o [portal Azure](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), [PowerShell](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) ou o [CLI](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). 

## <a name="managing-linked-storage-accounts"></a>Gestão de contas de armazenamento ligadas

Para ligar ou desvincular as contas de armazenamento ao seu espaço de trabalho utilize a [ALI Azure CLI](/cli/azure/monitor/log-analytics/workspace/linked-storage) ou [a REST API](/rest/api/loganalytics/linkedstorageaccounts).

##### <a name="create-or-modify-a-link"></a>Criar ou modificar um link
Quando liga uma conta de armazenamento a um espaço de trabalho, o Log Analytics começará a usá-la em vez da conta de armazenamento detida pelo serviço. É possível 
* Registar várias contas de armazenamento para espalhar a carga de registos entre eles
* Reutilizar a mesma conta de armazenamento para vários espaços de trabalho

##### <a name="unlink-a-storage-account"></a>Desvincular uma conta de armazenamento
Para parar de usar uma conta de armazenamento, desvincula o armazenamento do espaço de trabalho. Desvincular todas as contas de armazenamento de um espaço de trabalho significa que o Log Analytics tentará contar com contas de armazenamento geridas pelo serviço. Se a sua rede tiver acesso limitado à internet, estes armazenamentos podem não estar disponíveis e qualquer cenário que dependa do armazenamento falhará.

##### <a name="replace-a-storage-account"></a>Substitua uma conta de armazenamento
Para substituir uma conta de armazenamento utilizada para a ingestão,
1.  **Criar uma ligação para uma nova conta de armazenamento.** Os agentes de registo receberão a configuração atualizada e começarão a enviar dados para o novo armazenamento também. O processo pode demorar alguns minutos.
2.  **Em seguida, desvincular a velha conta de armazenamento para que os agentes deixem de escrever na conta removida.** O processo de ingestão mantém os dados de leitura desta conta até que tudo seja ingerido. Não apague a conta de armazenamento até ver que todos os registos foram ingeridos.

### <a name="maintaining-storage-accounts"></a>Manutenção de contas de armazenamento
##### <a name="manage-log-retention"></a>Gerir a retenção de registos
Ao utilizar a sua própria conta de armazenamento, a retenção é consigo. Por outras palavras, o Log Analytics não elimina os registos armazenados no seu armazenamento privado. Em vez disso, deverá configurar uma política para lidar com a carga de acordo com as suas preferências.

##### <a name="consider-load"></a>Considere a carga
As contas de armazenamento podem lidar com uma certa carga de pedidos de leitura e escrita antes de começarem a estrangular os pedidos (ver [scalability e alvos de desempenho para armazenamento Blob](../../storage/common/scalability-targets-standard-account.md) para mais detalhes). A aceleração afeta o tempo que leva a ingerir troncos. Se a sua conta de armazenamento estiver sobrecarregada, registe uma conta de armazenamento adicional para espalhar a carga entre eles. Para monitorizar a capacidade e o desempenho da sua conta de armazenamento, reveja os seus [Insights no portal Azure]( https://docs.microsoft.com/azure/azure-monitor/insights/storage-insights-overview).

### <a name="related-charges"></a>Encargos relacionados
As contas de armazenamento são cobradas pelo volume de dados armazenados, pelo tipo de armazenamento e pelo tipo de redundância. Para mais detalhes consulte [os preços do blob do Bloco](https://azure.microsoft.com/pricing/details/storage/blobs) e os preços de armazenamento de [mesa](https://azure.microsoft.com/pricing/details/storage/tables).


## <a name="next-steps"></a>Passos seguintes

- Saiba como [utilizar o Azure Private Link para ligar de forma segura as redes ao Azure Monitor](private-link-security.md)
- Saiba mais sobre [as chaves geridas pelo cliente do Azure Monitor](customer-managed-keys.md)
