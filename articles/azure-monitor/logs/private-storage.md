---
title: Utilizar contas de armazenamento geridas pelo cliente no Log Analytics do Azure Monitor
description: Use a sua própria conta de armazenamento para cenários de Log Analytics
ms.subservice: logs
ms.topic: conceptual
author: noakup
ms.author: noakuper
ms.date: 09/03/2020
ms.openlocfilehash: 4161f2f4ced848eb02d395dfb2da35d64f0c0fb6
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101723066"
---
# <a name="using-customer-managed-storage-accounts-in-azure-monitor-log-analytics"></a>Utilizar contas de armazenamento geridas pelo cliente no Log Analytics do Azure Monitor

O Log Analytics conta com o Azure Storage em vários cenários. Esta utilização é normalmente gerida automaticamente. No entanto, alguns casos exigem que você forneça e gere a sua própria conta de armazenamento, também referida como uma conta de armazenamento gerida pelo cliente. Este documento abrange a utilização de armazenamento gerido pelo cliente para registos WAD/LAD, Ligação Privada e encriptação de chaves gerida pelo cliente (CMK). 

> [!NOTE]
> Recomendamos que não assuma uma dependência dos conteúdos que o Log Analytics carrega para armazenamento gerido pelo cliente, dado que a formatação e o conteúdo podem mudar.

## <a name="ingesting-azure-diagnostics-extension-logs-wadlad"></a>Ingerir registos de extensão de Diagnóstico Azure (WAD/LAD)
Os agentes de extensão Azure Diagnostics (também chamados WAD e LAD para agentes Windows e Linux, respectivamente) recolhem vários registos do sistema operativo e armazenam-nos numa conta de armazenamento gerida pelo cliente. Em seguida, pode ingerir estes registos no Log Analytics para analisá-los e analisá-los.
### <a name="how-to-collect-azure-diagnostics-extension-logs-from-your-storage-account"></a>Como recolher registos de extensão Azure Diagnostics da sua conta de armazenamento
Ligue a conta de armazenamento ao seu espaço de trabalho Log Analytics como fonte de dados de armazenamento utilizando [o portal Azure](../essentials/diagnostics-extension-logs.md#collect-logs-from-azure-storage) ou ligando para a [API de Insights de Armazenamento](/rest/api/loganalytics/storage%20insights/createorupdate).

Tipos de dados suportados:
* Syslog
* Eventos do Windows
* Service Fabric
* Eventos DA ETW
* Registos do IIS

## <a name="using-private-links"></a>Utilização de links privados
As contas de armazenamento geridas pelo cliente são usadas para ingerir registos personalizados ou registos IIS quando ligações privadas são usadas para ligar aos recursos do Azure Monitor. O processo de ingestão destes tipos de dados primeiro envia registos para uma conta de Armazenamento Azure intermediário, e só depois os ingere num espaço de trabalho. 

### <a name="using-a-customer-managed-storage-account-over-a-private-link"></a>Usando uma conta de armazenamento gerida pelo cliente através de um Link Privado
#### <a name="workspace-requirements"></a>Requisitos do espaço de trabalho
Ao ligar-se ao Azure Monitor através de uma ligação privada, os agentes do Log Analytics só são capazes de enviar registos para espaços de trabalho acessíveis através de um link privado. Este requisito significa que deve:
* Configure um objeto Azure Monitor Private Link Scope (AMPLS)
* Conecte-o aos seus espaços de trabalho
* Ligue os AMPLS à sua rede por uma ligação privada. 

Para obter mais informações sobre o procedimento de configuração AMPLS, consulte [utilizar o Link Privado Azure para ligar de forma segura as redes ao Azure Monitor](./private-link-security.md). 

#### <a name="storage-account-requirements"></a>Requisitos da conta de armazenamento
Para que a conta de armazenamento se conecte com sucesso ao seu link privado, deve:
* Esteja localizado no seu VNet ou numa rede espreitada e ligado ao seu VNet por um link privado.
* Localiza-se na mesma região que o espaço de trabalho a que está ligado.
* Permitir ao Monitor Azure aceder à conta de armazenamento. Se optar por permitir apenas que redes selecionadas acedam à sua conta de armazenamento, deverá selecionar a exceção: "Permitir que serviços fidedignos da Microsoft acedam a esta conta de armazenamento".
![Imagem de serviços de MS fidedindo de conta de armazenamento](./media/private-storage/storage-trust.png)
* Se o seu espaço de trabalho também lidar com o tráfego de outras redes, deverá configurar a conta de armazenamento para permitir a entrada de tráfego proveniente das redes/internet relevantes.
* Coordenar a versão TLS entre os agentes e a conta de armazenamento - É recomendável que envie dados para Log Analytics utilizando tLS 1.2 ou superior. Reveja [a orientação específica da plataforma](https://docs.microsoft.com/azure/azure-monitor/logs/data-security#sending-data-securely-using-tls-12), e se necessário [configurar os seus agentes para utilizar o TLS 1.2](https://docs.microsoft.com/azure/azure-monitor/agents/agent-windows#configure-agent-to-use-tls-12). Se por alguma razão isso não for possível, configuure a conta de armazenamento para aceitar TLS 1.0.

### <a name="using-a-customer-managed-storage-account-for-cmk-data-encryption"></a>Utilização de uma conta de armazenamento gerida pelo cliente para encriptação de dados CMK
O Azure Storage encripta todos os dados em repouso numa conta de armazenamento. Por padrão, utiliza as teclas geridas pela Microsoft (MMK) para encriptar os dados; No entanto, o Azure Storage também permite utilizar o CMK do cofre Azure Key para encriptar os seus dados de armazenamento. Pode importar as suas próprias chaves para o Cofre da Chave Azure, ou pode usar as APIs do Cofre de Chaves Azure para gerar chaves.
#### <a name="cmk-scenarios-that-require-a-customer-managed-storage-account"></a>Cenários CMK que requerem uma conta de armazenamento gerida pelo cliente
* Encriptação de consultas de alerta de registo com CMK
* Encriptação de consultas guardadas com CMK

#### <a name="how-to-apply-cmk-to-customer-managed-storage-accounts"></a>Como aplicar CMK a contas de armazenamento geridas pelo cliente
##### <a name="storage-account-requirements"></a>Requisitos da conta de armazenamento
A conta de armazenamento e o cofre-chave devem estar na mesma região, mas podem estar em diferentes subscrições. Para obter mais informações sobre encriptação do Azure Storage e gestão de chaves, consulte [a encriptação do Armazenamento Azure para obter dados em repouso](../../storage/common/storage-service-encryption.md).

##### <a name="apply-cmk-to-your-storage-accounts"></a>Aplicar CMK nas suas contas de armazenamento
Para configurar a sua conta de Armazenamento Azure para utilizar CMK com cofre de chave Azure, utilize o [portal Azure](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json), [PowerShell](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json), ou o [CLI](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json). 

## <a name="link-storage-accounts-to-your-log-analytics-workspace"></a>Ligue as contas de armazenamento ao seu espaço de trabalho Log Analytics
### <a name="using-the-azure-portal"></a>Utilizar o portal do Azure
No portal Azure, abra o menu do espaço de trabalho e selecione *contas de armazenamento ligadas.* Abrir-se-á uma lâmina, mostrando as contas de armazenamento ligadas pelos casos de utilização acima mencionados (Ingestão sobre Ligação Privada, aplicação de CMK a consultas guardadas ou a alertas).
![Imagem de lâmina de contas de armazenamento ](./media/private-storage/all-linked-storage-accounts.png) ligadaS Selecionar um item na tabela abrirá os detalhes da sua conta de armazenamento, onde pode definir ou atualizar a conta de armazenamento ligada para este tipo. 
![Ligue uma imagem da lâmina da conta de armazenamento ](./media/private-storage/link-a-storage-account-blade.png) Pode utilizar a mesma conta para diferentes casos de utilização, se preferir.

### <a name="using-the-azure-cli-or-rest-api"></a>Utilizando a ALI Azure ou REST API
Também pode ligar uma conta de armazenamento ao seu espaço de trabalho através do [Azure CLI](/cli/azure/monitor/log-analytics/workspace/linked-storage) ou [REST API.](/rest/api/loganalytics/linkedstorageaccounts)

Os valores de DataSourceType aplicáveis são:
* CustomLogs – para utilizar a conta de armazenamento para registos personalizados e ingestão de registos IIS
* Consulta - utilizar a conta de armazenamento para armazenar consultas guardadas (necessárias para encriptação CMK)
* Alertas - utilizar a conta de armazenamento para armazenar alertas baseados em registos (necessários para encriptação CMK)


## <a name="managing-linked-storage-accounts"></a>Gestão de contas de armazenamento ligadas

### <a name="create-or-modify-a-link"></a>Criar ou modificar um link
Quando liga uma conta de armazenamento a um espaço de trabalho, o Log Analytics começará a usá-la em vez da conta de armazenamento detida pelo serviço. É possível 
* Registar várias contas de armazenamento para espalhar a carga de registos entre eles
* Reutilizar a mesma conta de armazenamento para vários espaços de trabalho

### <a name="unlink-a-storage-account"></a>Desvincular uma conta de armazenamento
Para parar de usar uma conta de armazenamento, desvincula o armazenamento do espaço de trabalho. Desvincular todas as contas de armazenamento de um espaço de trabalho significa que o Log Analytics tentará contar com contas de armazenamento geridas pelo serviço. Se a sua rede tiver acesso limitado à internet, estes armazenamentos podem não estar disponíveis e qualquer cenário que dependa do armazenamento falhará.

### <a name="replace-a-storage-account"></a>Substitua uma conta de armazenamento
Para substituir uma conta de armazenamento utilizada para a ingestão,
1.  **Criar uma ligação para uma nova conta de armazenamento.** Os agentes de registo receberão a configuração atualizada e começarão a enviar dados para o novo armazenamento também. O processo pode demorar alguns minutos.
2.  **Em seguida, desvincular a velha conta de armazenamento para que os agentes deixem de escrever na conta removida.** O processo de ingestão mantém os dados de leitura desta conta até que tudo seja ingerido. Não apague a conta de armazenamento até ver que todos os registos foram ingeridos.

### <a name="maintaining-storage-accounts"></a>Manutenção de contas de armazenamento
#### <a name="manage-log-retention"></a>Gerir a retenção de registos
Ao utilizar a sua própria conta de armazenamento, a retenção é consigo. O Log Analytics não eliminará os registos armazenados no seu armazenamento privado. Em vez disso, deverá definir uma política para lidar com a carga de acordo com as suas preferências.

#### <a name="consider-load"></a>Considere a carga
As contas de armazenamento podem lidar com uma certa carga de pedidos de leitura e de escrita antes de começarem a estrangular os pedidos (Para mais informações, ver [scalability e alvos de desempenho para armazenamento blob).](../../storage/common/scalability-targets-standard-account.md) A aceleração afeta o tempo que leva a ingerir troncos. Se a sua conta de armazenamento estiver sobrecarregada, registe uma conta de armazenamento adicional para espalhar a carga entre eles. Para monitorizar a capacidade e o desempenho da sua conta de armazenamento, reveja os seus [Insights no portal Azure]( https://docs.microsoft.com/azure/azure-monitor/insights/storage-insights-overview).

### <a name="related-charges"></a>Encargos relacionados
As contas de armazenamento são cobradas pelo volume de dados armazenados, pelo tipo de armazenamento e pelo tipo de redundância. Para mais detalhes consulte [os preços do blob do Bloco](https://azure.microsoft.com/pricing/details/storage/blobs) e os preços de armazenamento de [mesa](https://azure.microsoft.com/pricing/details/storage/tables).


## <a name="next-steps"></a>Passos seguintes

- Saiba como [utilizar o Azure Private Link para ligar de forma segura as redes ao Azure Monitor](private-link-security.md)
- Saiba mais sobre [as chaves geridas pelo cliente do Azure Monitor](../logs/customer-managed-keys.md)
