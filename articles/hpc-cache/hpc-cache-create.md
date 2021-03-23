---
title: Criar uma Cache Azure HPC
description: Como criar uma instância cache Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/30/2020
ms.author: v-erkel
ms.openlocfilehash: 63a179f7123f088c9c60fbfb8996a5b21d1c704d
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104773271"
---
# <a name="create-an-azure-hpc-cache"></a>Criar uma Cache Azure HPC

Utilize o portal Azure ou o CLI Azure para criar o seu cache.

![screenshot da visão geral da cache no portal Azure, com o botão criar na parte inferior](media/hpc-cache-home-page.png)

Clique na imagem abaixo para ver uma [demonstração](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/) de vídeo de criar uma cache e adicionar um alvo de armazenamento.

[![miniatura de vídeo: Azure HPC Cache: Configuração (clique para visitar a página de vídeo)](media/video-4-setup.png)](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/)

## <a name="portal"></a>[Portal](#tab/azure-portal)

## <a name="define-basic-details"></a>Definir detalhes básicos

![screenshot da página de detalhes do projeto no portal Azure](media/hpc-cache-create-basics.png)

Em **Detalhes do Projeto,** selecione o grupo de subscrição e recursos que irá acolher a cache.

Em **Detalhes de Serviço,** desateie o nome da cache e estes outros atributos:

* Localização - Selecione uma das [regiões apoiadas.](hpc-cache-overview.md#region-availability)
* Rede virtual - Pode selecionar uma existente ou criar uma nova rede virtual.
* Sub-rede - Escolha ou crie uma sub-rede com pelo menos 64 endereços IP (/24). Esta sub-rede deve ser utilizada apenas para esta instância cache Azure HPC.

## <a name="set-cache-capacity"></a>Definir capacidade de cache
<!-- referenced from GUI - update aka.ms link if you change this header text -->

Na página **Cache,** deve definir a capacidade do seu cache. Os valores aqui definidos determinam a quantidade de dados que o seu cache pode reter e a rapidez com que pode atender os pedidos do cliente.

A capacidade também afeta o custo do cache.

Escolha a capacidade definindo estes dois valores:

* A taxa máxima de transferência de dados para a cache (produção), em GB/segundo
* A quantidade de armazenamento atribuída aos dados em cache, em Tuberculose

Escolha um dos valores de produção disponíveis e tamanhos de armazenamento em cache.

Tenha em mente que a taxa real de transferência de dados depende da carga de trabalho, das velocidades de rede e do tipo de alvos de armazenamento. Os valores que escolher definem a produção máxima para todo o sistema de cache, mas alguns deles são utilizados para tarefas aéreas. Por exemplo, se um cliente solicitar um ficheiro que já não esteja armazenado na cache, ou se o ficheiro estiver marcado como velho, o seu cache utiliza parte da sua produção para o ir buscar ao armazenamento traseiro.

A Azure HPC Cache gere quais os ficheiros em cache e pré-carregados para maximizar as taxas de certeamento de cache. Os conteúdos da cache são continuamente avaliados e os ficheiros são transferidos para armazenamento a longo prazo quando são menos acessados. Escolha um tamanho de armazenamento de cache que possa confortavelmente segurar o conjunto ativo de ficheiros de trabalho, além de espaço adicional para metadados e outras despesas gerais.

![screenshot da página de tamanho cache](media/hpc-cache-create-capacity.png)

## <a name="enable-azure-key-vault-encryption-optional"></a>Ativar encriptação do cofre da chave Azure (opcional)

A página **das chaves de encriptação** do disco aparece entre os separadores **Cache** e **Tags.**<!-- Read [Regional availability](hpc-cache-overview.md#region-availability) to learn more about region support. -->

Se pretender gerir as chaves de encriptação utilizadas para o armazenamento do cache, forneça as informações do Cofre da Chave Azure na página das **chaves de encriptação** do disco. O cofre-chave deve estar na mesma região e na mesma subscrição que a cache.

Pode saltar esta secção se não precisar de chaves geridas pelo cliente. O Azure encripta os dados com as teclas geridas pela Microsoft por padrão. Leia [a encriptação de armazenamento Azure](../storage/common/storage-service-encryption.md) para saber mais.

> [!NOTE]
>
> * Não é possível alterar entre as teclas geridas pela Microsoft e as teclas geridas pelo cliente depois de criar o cache.
> * Depois da cache ser criada, deve autorizar o acesso ao cofre da chave. Clique no botão **de encriptação Ativar** na página **de visão geral** do cache para ativar a encriptação. Dê este passo dentro de 90 minutos após a criação da cache.
> * Os discos cache são criados após esta autorização. Isto significa que o tempo inicial de criação da cache é curto, mas a cache não estará pronta para ser usada por dez minutos ou mais depois de autorizar o acesso.

Para obter uma explicação completa do processo de encriptação da chave gerido pelo cliente, leia [Use as chaves de encriptação geridas pelo cliente para a cache Azure HPC](customer-keys.md).

![screenshot da página de chaves de encriptação com "cliente gerido" selecionado e campos de cofre chave mostrando](media/create-encryption.png)

O Cliente Selecionado **conseguiu** escolher a encriptação de chave gerida pelo cliente. Os campos de especificação do cofre aparecem. Selecione o Cofre da Chave Azure para utilizar e, em seguida, selecione a chave e a versão a utilizar para esta cache. A chave deve ser uma chave RSA de 2048. Pode criar um novo cofre, chave ou versão chave a partir desta página.

Depois de criar a cache, deve autorizar a utilização do serviço de cofre de chaves. Leia [a encriptação do Cofre da Chave Azure autorizada a partir da cache](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) para obter mais detalhes.

## <a name="add-resource-tags-optional"></a>Adicionar etiquetas de recursos (opcional)

A página **Tags** permite adicionar [etiquetas de recursos](../azure-resource-manager/management/tag-resources.md) à sua instância cache Azure HPC.

## <a name="finish-creating-the-cache"></a>Termine de criar a cache

Depois de configurar a nova cache, clique no **separador 'Rever +' criar.** O portal valida as suas seleções e permite-lhe rever as suas escolhas. Se tudo estiver correto, clique em **Criar**.

A criação de cache leva cerca de 10 minutos. Pode acompanhar o progresso no painel de notificações do portal Azure.

![screenshot da criação de cache "implantação em curso" e "notificações" páginas no portal](media/hpc-cache-deploy-status.png)

Quando a criação termina, uma notificação aparece com um link para a nova instância Azure HPC Cache, e a cache aparece na lista **de Recursos** da sua subscrição.

![screenshot de Azure HPC Cache instância no portal Azure](media/hpc-cache-new-overview.png)

> [!NOTE]
> Se o seu cache utilizar chaves de encriptação geridas pelo cliente, a cache pode aparecer na lista de recursos antes que o estado de implantação seja concluído. Assim que o estado da cache estiver à espera da **chave,** pode [autorizar a](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) utilização do cofre da chave.

## <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

## <a name="create-the-cache-with-azure-cli"></a>Crie a cache com Azure CLI

[Configurar o Azure CLI para a cache Azure HPC](./az-cli-prerequisites.md).

> [!NOTE]
> Atualmente, o CLI Azure não suporta a criação de um cache com chaves de encriptação geridas pelo cliente. Utilize o portal do Azure.

Utilize o comando [az hpc-cache](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-create) para criar uma nova Cache Azure HPC.

Fornecer estes valores:

* Nome do grupo de recursos cache
* Nome cache
* Região do Azure
* Sub-rede cache, neste formato:

  ``--subnet "/subscriptions/<subscription_id>/resourceGroups/<cache_resource_group>/providers/Microsoft.Network/virtualNetworks/<virtual_network_name>/sub
nets/<cache_subnet_name>"``

  A sub-rede cache precisa de pelo menos 64 endereços IP (/24), e não pode alojar quaisquer outros recursos.

* Capacidade de cache. Dois valores definem a produção máxima da sua Cache Azure HPC:

  * O tamanho da cache (em GB)
  * O SKU das máquinas virtuais utilizadas na infraestrutura de cache

  [az hpc-cache skus lista](/cli/azure/ext/hpc-cache/hpc-cache/skus) mostra as SKUs disponíveis e as opções de tamanho de cache válidos para cada um. As opções de tamanho da cache variam de 3 TB a 48 TB, mas apenas alguns valores são suportados.

  Este gráfico mostra qual o tamanho da cache e as combinações SKU válidas no momento em que este documento está a ser preparado (julho de 2020).

  | Tamanho da cache | Standard_2G | Standard_4G | Standard_8G |
  |------------|-------------|-------------|-------------|
  | 3072 GB    | sim         | não          | não          |
  | 6144 GB    | sim         | sim         | não          |
  | 12288 GB   | sim         | sim         | sim         |
  | 24576 GB   | não          | sim         | sim         |
  | 49152 GB   | não          | não          | sim         |

  Leia a secção **de capacidade de cache definida** no separador instruções do portal para obter informações importantes sobre preços, saídas e como dimensionar adequadamente a sua cache para o seu fluxo de trabalho.

Exemplo de criação de cache:

```azurecli
az hpc-cache create --resource-group doc-demo-rg --name my-cache-0619 \
    --location "eastus" --cache-size-gb "3072" \
    --subnet "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default" \
    --sku-name "Standard_2G"
```

A criação de cache leva vários minutos. No sucesso, o comando criar retorna a saída como esta:

```azurecli
{
  "cacheSizeGb": 3072,
  "health": {
    "state": "Healthy",
    "statusDescription": "The cache is in Running state"
  },
  "id": "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.StorageCache/caches/my-cache-0619",
  "location": "eastus",
  "mountAddresses": [
    "10.3.0.17",
    "10.3.0.18",
    "10.3.0.19"
  ],
  "name": "my-cache-0619",
  "provisioningState": "Succeeded",
  "resourceGroup": "doc-demo-rg",
  "sku": {
    "name": "Standard_2G"
  },
  "subnet": "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default",
  "tags": null,
  "type": "Microsoft.StorageCache/caches",
  "upgradeStatus": {
    "currentFirmwareVersion": "5.3.42",
    "firmwareUpdateDeadline": "0001-01-01T00:00:00+00:00",
    "firmwareUpdateStatus": "unavailable",
    "lastFirmwareUpdate": "2020-04-01T15:19:54.068299+00:00",
    "pendingFirmwareVersion": null
  }
}
```

A mensagem inclui algumas informações úteis, incluindo estes itens:

* Endereços de montagem do cliente - Utilize estes endereços IP quando estiver pronto para ligar os clientes à cache. Leia [o Monte da Cache Azure HPC](hpc-cache-mount.md) para saber mais.
* Estado de atualização - Quando uma atualização de software é lançada, esta mensagem mudará. Pode [atualizar o software cache](hpc-cache-manage.md#upgrade-cache-software) manualmente numa hora conveniente, ou será aplicado automaticamente após vários dias.

## <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

> [!CAUTION]
> O módulo Az.HPCCache PowerShell está atualmente em pré-visualização pública. Esta versão de pré-visualização é disponibilizada sem contrato de nível de serviço. Não é recomendada para cargas de trabalho de produção. Algumas funcionalidades podem não ser suportadas ou podem ter capacidades restritas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="requirements"></a>Requisitos

Se optar por utilizar o PowerShell localmente, este artigo requer que instale o módulo Az PowerShell e se conecte à sua conta Azure utilizando o cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount) Para obter mais informações sobre a instalação do módulo Az PowerShell, consulte [instalar a Azure PowerShell](/powershell/azure/install-az-ps). Se optar por utilizar a Cloud Shell, consulte [a visão geral da Azure Cloud Shell](../cloud-shell/overview.md) para obter mais informações.

> [!IMPORTANT]
> Enquanto o módulo **Az.HPCCache** PowerShell estiver em pré-visualização, deve instalá-lo separadamente utilizando o `Install-Module` cmdlet. Depois deste módulo PowerShell ficar geralmente disponível, fará parte dos futuros lançamentos do módulo Az PowerShell e estará disponível de forma nativa a partir de Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.HPCCache
```

## <a name="create-the-cache-with-azure-powershell"></a>Crie a cache com Azure PowerShell

> [!NOTE]
> A Azure PowerShell não suporta atualmente a criação de um cache com chaves de encriptação geridas pelo cliente. Utilize o portal do Azure.

Utilize o [cmdlet New-AzHpcCache](/powershell/module/az.hpccache/new-azhpccache) para criar uma nova Cache Azure HPC.

Fornecer estes valores:

* Nome do grupo de recursos cache
* Nome cache
* Região do Azure
* Sub-rede cache, neste formato:

  `-SubnetUri "/subscriptions/<subscription_id>/resourceGroups/<cache_resource_group>/providers/Microsoft.Network/virtualNetworks/<virtual_network_name>/sub
nets/<cache_subnet_name>"`

  A sub-rede cache precisa de pelo menos 64 endereços IP (/24), e não pode alojar quaisquer outros recursos.

* Capacidade de cache. Dois valores definem a produção máxima da sua Cache Azure HPC:

  * O tamanho da cache (em GB)
  * O SKU das máquinas virtuais utilizadas na infraestrutura de cache

  [O Get-AzHpcCacheSku](/powershell/module/az.hpccache/get-azhpccachesku) mostra os SKUs disponíveis e as opções de tamanho de cache válidas para cada um. As opções de tamanho da cache variam de 3 TB a 48 TB, mas apenas alguns valores são suportados.

  Este gráfico mostra qual o tamanho da cache e as combinações SKU válidas no momento em que este documento está a ser preparado (julho de 2020).

  | Tamanho da cache | Standard_2G | Standard_4G | Standard_8G |
  |------------|-------------|-------------|-------------|
  | 3072 GB    | sim         | não          | não          |
  | 6144 GB    | sim         | sim         | não          |
  | 12.288 GB   | sim         | sim         | sim         |
  | 24,576 GB   | não          | sim         | sim         |
  | 49,152 GB   | não          | não          | sim         |

  Leia a secção **de capacidade de cache definida** no separador instruções do portal para obter informações importantes sobre preços, saídas e como dimensionar adequadamente a sua cache para o seu fluxo de trabalho.

Exemplo de criação de cache:

```azurepowershell-interactive
$cacheParams = @{
  ResourceGroupName = 'doc-demo-rg'
  CacheName = 'my-cache-0619'
  Location = 'eastus'
  cacheSize = '3072'
  SubnetUri = "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default"
  Sku = 'Standard_2G'
}
New-AzHpcCache @cacheParams
```

A criação de cache leva vários minutos. No sucesso, o comando criar devolve a seguinte saída:

```Output
cacheSizeGb       : 3072
health            : @{state=Healthy; statusDescription=The cache is in Running state}
id                : /subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.StorageCache/caches/my-cache-0619
location          : eastus
mountAddresses    : {10.3.0.17, 10.3.0.18, 10.3.0.19}
name              : my-cache-0619
provisioningState : Succeeded
resourceGroup     : doc-demo-rg
sku               : @{name=Standard_2G}
subnet            : /subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default
tags              :
type              : Microsoft.StorageCache/caches
upgradeStatus     : @{currentFirmwareVersion=5.3.42; firmwareUpdateDeadline=1/1/0001 12:00:00 AM; firmwareUpdateStatus=unavailable; lastFirmwareUpdate=4/1/2020 10:19:54 AM; pendingFirmwareVersion=}
```

A mensagem inclui algumas informações úteis, incluindo estes itens:

* Endereços de montagem do cliente - Utilize estes endereços IP quando estiver pronto para ligar os clientes à cache. Leia [o Monte da Cache Azure HPC](hpc-cache-mount.md) para saber mais.
* Estado de atualização - Quando uma atualização de software é lançada, esta mensagem muda. Pode [atualizar](hpc-cache-manage.md#upgrade-cache-software) o software cache manualmente numa hora conveniente, ou é aplicado automaticamente após vários dias.

---

## <a name="next-steps"></a>Passos seguintes

Depois da sua cache aparecer na lista **de Recursos,** pode passar para o próximo passo.

* [Defina os alvos de armazenamento](hpc-cache-add-storage.md) para dar acesso ao seu cache às suas fontes de dados.
* Se utilizar as chaves de encriptação geridas pelo cliente, tem de autorizar a encriptação do Cofre da [Chave Azure](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) a partir da página geral do cache para completar a configuração do cache. Tem de dar este passo antes de poder adicionar armazenamento. Leia [Utilize as chaves de encriptação geridas pelo cliente](customer-keys.md) para obter detalhes.
