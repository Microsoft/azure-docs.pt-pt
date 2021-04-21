---
title: Criar uma conta de armazenamento
titleSuffix: Azure Storage
description: Aprenda a criar uma conta de armazenamento para armazenar bolhas, ficheiros, filas e mesas. Uma conta de armazenamento Azure fornece um espaço de nome único no Microsoft Azure para ler e escrever os seus dados.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/19/2021
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 8c0158ea4e93b4bbefa7b41b5a280fa804ede35f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791288"
---
# <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Uma conta de armazenamento Azure contém todos os seus objetos de dados de armazenamento Azure: bolhas, ficheiros, filas e tabelas. A conta de armazenamento fornece um espaço de nome único para os seus dados de Armazenamento Azure que estão acessíveis a partir de qualquer parte do mundo em HTTP ou HTTPS. Para obter mais informações sobre as contas de armazenamento Azure, consulte [a visão geral da conta de Armazenamento](storage-account-overview.md).

Neste artigo de como fazer, aprende-se a criar uma conta de armazenamento utilizando o [portal Azure](https://portal.azure.com/) [, Azure PowerShell,](/powershell/azure/) [Azure CLI,](/cli/azure)ou um [modelo Azure Resource Manager](../../azure-resource-manager/management/overview.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Nenhum.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para criar uma conta de armazenamento Azure com o PowerShell, certifique-se de que instalou o [módulo Az PowerShell](https://www.powershellgallery.com/packages/Az), versão 0.7 ou posterior. Para obter mais informações, consulte [a introdução do módulo Azure PowerShell Az](/powershell/azure/new-azureps-module-az).

Para encontrar a sua versão atual, executar o seguinte comando:

```powershell
Get-InstalledModule -Name "Az"
```

Para instalar ou atualizar o Azure PowerShell, consulte [instalar o módulo Azure PowerShell](/powershell/azure/install-az-ps).

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Pode entrar no Azure e executar os comandos Azure CLI de uma de duas maneiras:

- Pode executar comandos CLI a partir do portal Azure, em Azure Cloud Shell.
- Pode instalar o CLI e executar os comandos CLI localmente.

### <a name="use-azure-cloud-shell"></a>Utilizar o Azure Cloud Shell

O Azure Cloud Shell é um shell Bash gratuito que pode ser executado diretamente no portal do Azure. O Azure CLI está pré-instalado e configurado para ser utilizado com a sua conta. Clique no botão **Cloud Shell** no menu na secção superior direita do portal Azure:

[![Cloud Shell](./media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

O botão lança uma concha interativa que pode usar para executar os passos descritos neste artigo de como fazer:

[![Screenshot mostrando a janela Cloud Shell no portal](./media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>Instalar a CLI localmente

Também pode instalar e utilizar a CLI do Azure localmente. Os exemplos deste artigo requerem a versão 2.0.4 ou posterior do Azure CLI. Corra `az --version` para encontrar a sua versão instalada. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

# <a name="template"></a>[Modelo](#tab/template)

Nenhum.

---

Em seguida, inicie sessão no Azure.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Inicie sessão no [portal do Azure](https://portal.azure.com).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Inscreva-se na sua subscrição Azure com o `Connect-AzAccount` comando e siga as instruções no ecrã para autenticar.

```powershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para lançar a Azure Cloud Shell, inscreva-se no [portal Azure](https://portal.azure.com).

Para iniciar sessão na instalação local do CLI, execute o comando [de login az:](/cli/azure/reference-index#az_login)

```azurecli-interactive
az login
```

# <a name="template"></a>[Modelo](#tab/template)

N/D

---

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Uma conta de armazenamento é um recurso Azure Resource Manager. Gestor de Recursos é o serviço de implementação e gestão da Azure. Para obter mais informações, veja [Descrição geral do Azure Resource Manager](../../azure-resource-manager/management/overview.md).

Todos os recursos do Gestor de Recursos, incluindo uma conta de armazenamento Azure, devem pertencer a um grupo de recursos Azure. Um grupo de recursos é um contentor lógico para agrupar os seus serviços do Azure. Quando cria uma conta de armazenamento, tem a opção de criar um novo grupo de recursos ou utilizar um grupo de recursos existente. Este como-mostrar como criar um novo grupo de recursos.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Para criar uma conta de armazenamento Azure com o portal Azure, siga estes passos:

1. A partir do menu do portal esquerdo, selecione **contas de Armazenamento** para apresentar uma lista das suas contas de armazenamento.
1. Na página **de contas de Armazenamento,** selecione **New**.

As opções para a sua nova conta de armazenamento são organizadas em separadores na página **Criar uma conta de armazenamento.** As seguintes secções descrevem cada um dos separadores e as suas opções.

### <a name="basics-tab"></a>Separador básico

No **separador Básicos,** forneça as informações essenciais para a sua conta de armazenamento. Depois de completar o separador **Básicos,** pode optar por personalizar ainda mais a sua nova conta de armazenamento definindo opções nos outros separadores, ou pode selecionar **Rever + criar** para aceitar as opções predefinidas e proceder à validação e criação da conta.

A tabela seguinte descreve os campos no separador **Básicos.**

| Section | Campo | Obrigatório ou opcional | Description |
|--|--|--|--|
| Detalhes do projeto | Subscrição | Necessário | Selecione a subscrição para a nova conta de armazenamento. |
| Detalhes do projeto | Grupo de recursos | Necessário | Crie um novo grupo de recursos para esta conta de armazenamento ou selecione uma existente. Para obter mais informações, consulte [os grupos de recursos.](../../azure-resource-manager/management/overview.md#resource-groups) |
| Detalhes da instância | Nome da conta de armazenamento | Necessário | Escolha um nome único para a sua conta de armazenamento. Os nomes das contas de armazenamento têm de ter entre 3 e 24 carateres de comprimento e apenas podem conter números e letras minúsculas. |
| Detalhes da instância | Region | Necessário | Selecione a região apropriada para a sua conta de armazenamento. Para mais informações, consulte [Regiões e Zonas de Disponibilidade em Azure.](../../availability-zones/az-overview.md)<br /><br />Nem todas as regiões são suportadas para todos os tipos de contas de armazenamento ou configurações de despedimento. Para obter mais informações, veja [Redundância do Armazenamento do Microsoft Azure](storage-redundancy.md).<br /><br />A escolha da região pode ter um impacto na faturação. Para obter mais informações, consulte [a faturação da conta de armazenamento.](storage-account-overview.md#storage-account-billing) |
| Detalhes da instância | Desempenho | Necessário | Selecione **desempenho standard** para contas de armazenamento v2 de uso geral (predefinição). Este tipo de conta é recomendado pela Microsoft para a maioria dos cenários. Para obter mais informações, consulte [tipos de contas de armazenamento.](storage-account-overview.md#types-of-storage-accounts)<br /><br />Selecione **Premium** para cenários que exijam baixa latência. Depois de selecionar **Premium,** selecione o tipo de conta de armazenamento premium para criar. Estão disponíveis os seguintes tipos de contas de armazenamento premium: <ul><li>[Blobs de blocos](../blobs/storage-blob-performance-tiers.md)</li><li>[Partilhas de ficheiros](../files/storage-files-planning.md#management-concepts)</li><li>[Blobs de páginas](../blobs/storage-blob-pageblob-overview.md)</li></ul> |
| Detalhes da instância | Redundância | Necessário | Selecione a configuração de redundância desejada. Nem todas as opções de despedimento estão disponíveis para todos os tipos de contas de armazenamento em todas as regiões. Para obter mais informações sobre configurações de redundância, consulte [a redundância do Azure Storage](storage-redundancy.md).<br /><br />Se selecionar uma configuração geo-redundante (GRS ou GZRS), os seus dados são replicados num centro de dados numa região diferente. Para ler o acesso aos dados na região secundária, **selecione Faça o acesso aos dados disponíveis em caso de indisponibilidade regional.** |

A imagem a seguir mostra uma configuração padrão para uma nova conta de armazenamento.

:::image type="content" source="media/storage-account-create/create-account-basics-tab.png" alt-text="Screenshot mostrando uma configuração padrão para uma nova conta de armazenamento - Separador básico":::

### <a name="advanced-tab"></a>Separador avançado

No separador **Avançado,** pode configurar opções adicionais e modificar as definições predefinidas para a sua nova conta de armazenamento. Algumas destas opções também podem ser configuradas após a criação da conta de armazenamento, enquanto outras devem ser configuradas no momento da criação.

A tabela seguinte descreve os campos no separador **Advanced.**

| Section | Campo | Obrigatório ou opcional | Description |
|--|--|--|--|
| Segurança | Ativar a transferência segura | Opcional | Permitir uma transferência segura para exigir que os pedidos de entrada nesta conta de armazenamento sejam feitos apenas através de HTTPS (predefinição). Recomendado para uma segurança ótima. Para obter mais informações, consulte [Exigir uma transferência segura para garantir ligações seguras](storage-require-secure-transfer.md). |
| Segurança | Ativar encriptação de infraestruturas | Opcional | Por predefinição, a encriptação da infraestrutura não está ativada. Permitir a encriptação da infraestrutura encriptar os seus dados tanto ao nível do serviço como ao nível da infraestrutura. Para obter mais informações, consulte [Criar uma conta de armazenamento com encriptação de infraestrutura ativada para dupla encriptação de dados](infrastructure-encryption-enable.md). |
| Segurança | Permitir o acesso público ao blob | Opcional | Quando ativada, esta definição permite a um utilizador com as permissões adequadas para permitir o acesso público anónimo a um contentor na conta de armazenamento (predefinição). A desativação desta definição impede todo o acesso público anónimo à conta de armazenamento. Para obter mais informações, consulte [Prevent anonymous public read access to containers and blobs](../blobs/anonymous-read-access-prevent.md).<br> <br> Permitir o acesso público blob não disponibiliza dados blob para acesso público, a menos que o utilizador tome o passo adicional para configurar explicitamente a configuração de acesso público do recipiente. |
| Segurança | Permitir o acesso à chave da conta de armazenamento (pré-visualização) | Opcional | Quando ativada, esta definição permite que os clientes autorizem pedidos à conta de armazenamento utilizando as chaves de acesso à conta ou uma conta Azure Ative Directory (Azure AD) (predefinição). A desativação desta definição impede a autorização com as teclas de acesso à conta. Para obter mais informações, consulte [Prevent Shared Key autorização para uma conta de Armazenamento Azure (pré-visualização)](shared-key-authorization-prevent.md). |
| Segurança | Versão mínima do TLS | Necessário | Selecione a versão mínima de Segurança da Camada de Transporte (TLS) para pedidos de entrada na conta de armazenamento. O valor predefinido é a versão 1.2 do TLS. Quando definidos para o valor predefinido, os pedidos de entrada feitos através do TLS 1.0 ou TLS 1.1 são rejeitados. Para obter mais informações, consulte [Impor uma versão mínima exigida de Segurança da Camada de Transporte (TLS) para pedidos a uma conta de armazenamento.](transport-layer-security-configure-minimum-version.md) |
| Data Lake Storage Gen2 | Ativar espaço hierárquico de nomes | Opcional | Para utilizar esta conta de armazenamento para a azure Data Lake Storage Gen2 cargas de trabalho, configurar um espaço hierárquico de nomes. Para obter mais informações, consulte [Introdução ao Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md). |
| Armazenamento de blobs | Ativar a partilha de ficheiros de rede (NFS) v3 (pré-visualização) | Opcional | O NFS v3 fornece compatibilidade do sistema de ficheiros Linux à escala de armazenamento de objetos, permitindo aos clientes do Linux montar um contentor no armazenamento Blob a partir de uma Máquina Virtual Azure (VM) ou de um computador no local. Para obter mais informações, consulte [o suporte do protocolo 3.0 do Sistema de Ficheiros de Rede (NFS) 3.0 no armazenamento do Blob Azure Blob (pré-visualização)](../blobs/network-file-system-protocol-support.md). |
| Armazenamento de blobs | Camada de acesso | Necessário | Os níveis de acesso blob permitem-lhe armazenar dados blob da forma mais rentável, com base na utilização. Selecione o nível quente (predefinido) para obter dados frequentemente acedidos. Selecione o nível cool para dados pouco acedidos. Para mais informações, consulte [os níveis de acesso para armazenamento Azure Blob - quente, fresco e arquivo](../blobs/storage-blob-storage-tiers.md). |
| Ficheiros do Azure | Permitir partilha de ficheiros de grandes dimensões | Opcional | Disponível apenas para contas de armazenamento premium para ações de ficheiros. Para obter mais informações, consulte [Ativar as ações de ficheiros padrão até 100 TiB](../files/storage-files-planning.md#enable-standard-file-shares-to-span-up-to-100-tib). |
| Mesas e filas | Ativar o suporte para chaves geridas pelo cliente | Opcional | Para ativar o suporte para chaves geridas pelo cliente para mesas e filas, tem de selecionar esta definição no momento em que criar a conta de armazenamento. Para obter mais informações, consulte [Criar uma conta que suporte chaves geridas pelo cliente para tabelas e filas.](account-encryption-key-create.md) |

### <a name="networking-tab"></a>Separador de rede

No **separador Networking,** pode configurar definições de preferências de conectividade de rede e encaminhamento para a sua nova conta de armazenamento. Estas opções também podem ser configuradas após a criação da conta de armazenamento.

A tabela seguinte descreve os campos no **separador 'Rede'.**

| Section | Campo | Obrigatório ou opcional | Description |
|--|--|--|--|
| Conectividade de rede | Método de conectividade | Necessário | Por predefinição, o tráfego de rede de entrada é encaminhado para o ponto final público para a sua conta de armazenamento. Pode especificar que o tráfego deve ser encaminhado para o ponto final público através de uma rede virtual Azure. Também pode configurar pontos finais privados para a sua conta de armazenamento. Para obter mais informações, consulte [utilizar pontos finais privados para o armazenamento Azure](storage-private-endpoints.md). |
| Encaminhamento de rede | Preferência de encaminhamento | Necessário | A preferência de encaminhamento de rede especifica como o tráfego de rede é encaminhado para o ponto final público da sua conta de armazenamento de clientes através da internet. Por predefinição, uma nova conta de armazenamento utiliza o encaminhamento da rede microsoft. Também pode optar por encaminhar o tráfego de rede através do POP mais próximo da conta de armazenamento, o que pode reduzir os custos de networking. Para obter mais informações, consulte [a preferência de encaminhamento de rede para armazenamento Azure](network-routing-preference.md). |

### <a name="data-protection-tab"></a>Separador de proteção de dados

No separador **de proteção de dados,** pode configurar opções de proteção de dados para dados blob na sua nova conta de armazenamento.  Estas opções também podem ser configuradas após a criação da conta de armazenamento. Para obter uma visão geral das opções de proteção de dados no Azure Storage, consulte a [visão geral da proteção de dados](../blobs/data-protection-overview.md).

A tabela seguinte descreve os campos no **separador de proteção de dados.**

| Section | Campo | Obrigatório ou opcional | Description |
|--|--|--|--|
| Recuperação | Permitir a restauração pontual dos contentores | Opcional | A restauração pontual proporciona proteção contra a eliminação acidental ou a corrupção, permitindo-lhe restaurar os dados de blob de bloqueio a um estado anterior. Para obter mais informações, consulte [o ponto-a-tempo para restaurar as bolhas de blocos](../blobs/point-in-time-restore-overview.md).<br /><br />Permitir a restauração pontual também permite a versão blob, a eliminação macia e o feed de alteração de bolhas. Estas funcionalidades pré-requisitos podem ter um impacto de custos. Para obter mais informações, consulte [preços e faturação](../blobs/point-in-time-restore-overview.md#pricing-and-billing) para restaurar o ponto a tempo. |
| Recuperação | Ativar a eliminação recuperável para blobs | Opcional | A eliminação macia de blob protege uma bolha individual, instantâneo ou versão de eliminações ou substituições acidentais mantendo os dados eliminados no sistema durante um período de retenção especificado. Durante o período de retenção, pode restaurar um objeto apagado suave ao seu estado no momento em que foi eliminado. Para obter mais informações, consulte [Soft delete para bolhas](../blobs/soft-delete-blob-overview.md).<br /><br />A Microsoft recomenda permitir a eliminação suave do blob para as suas contas de armazenamento e definir um período mínimo de retenção de sete dias. |
| Recuperação | Ativar a eliminação suave para recipientes (pré-visualização) | Opcional | A eliminação suave do recipiente protege um recipiente e o seu conteúdo de eliminações acidentais mantendo os dados eliminados no sistema durante um período de retenção especificado. Durante o período de retenção, pode restaurar um recipiente de sossar suave no momento em que foi eliminado. Para obter mais informações, consulte [a exclusão suave para recipientes (pré-visualização)](../blobs/soft-delete-container-overview.md).<br /><br />A Microsoft recomenda que o contentor elimine suavemente os recipientes para as suas contas de armazenamento e que estabeleça um período mínimo de retenção de sete dias. |
| Recuperação | Ativar a eliminação suave para ações de ficheiros | Opcional | A eliminação suave para ações de ficheiros protege uma partilha de ficheiros e o seu conteúdo de eliminações acidentais mantendo os dados eliminados no sistema durante um período de retenção especificado. Durante o período de retenção, pode restaurar uma partilha de ficheiros comvidade no seu estado no momento em que foi eliminada. Para obter mais informações, consulte [Prevenir a eliminação acidental das ações de ficheiros Azure](../files/storage-files-prevent-file-share-deletion.md).<br /><br />A Microsoft recomenda permitir a eliminação suave das ações de ficheiros para as cargas de trabalho dos Ficheiros Azure e definir um período mínimo de retenção de sete dias. |
| Controlo | Ativar a versão para bolhas | Opcional | A versão blob salva automaticamente o estado de uma bolha numa versão anterior quando a bolha é substituída. Para obter mais informações, consulte [a versão Blob](../blobs/versioning-overview.md).<br /><br />A Microsoft recomenda a disponibilização de blob para uma proteção de dados ótima para a conta de armazenamento. |
| Controlo | Ativar o feed de alteração de blob | Opcional | O feed de alteração de blob fornece registos de transações de todas as alterações a todas as bolhas na sua conta de armazenamento, bem como aos seus metadados. Para obter mais informações, consulte [o suporte de feed change no Azure Blob Storage](../blobs/storage-blob-change-feed.md). |

### <a name="tags-tab"></a>Separador tags

No separador **Tags,** pode especificar etiquetas do Gestor de Recursos para ajudar a organizar os seus recursos Azure. Para obter mais informações, consulte [recursos tag, grupos de recursos e subscrições para organização lógica.](../../azure-resource-manager/management/tag-resources.md)

### <a name="review--create-tab"></a>Rever + criar separador

Quando navega no **separador 'Rever +' criar,** o Azure executa a validação nas definições da conta de armazenamento que escolheu. Se a validação passar, pode proceder à criação da conta de armazenamento.

Se a validação falhar, o portal indica quais as definições que precisam de ser modificadas.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para criar uma conta de armazenamento v2 para fins gerais com o PowerShell, primeiro crie um novo grupo de recursos chamando o comando [New-AzResourceGroup:](/powershell/module/az.resources/new-azresourcegroup)

```azurepowershell-interactive
$resourceGroup = "<resource-group>"
$location = "<location>"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

Se não tiver a certeza de qual região especificar para o `-Location` parâmetro, pode recuperar uma lista de regiões apoiadas para a sua subscrição com o comando [Get-AzLocation:](/powershell/module/az.resources/get-azlocation)

```azurepowershell-interactive
Get-AzLocation | select Location
```

Em seguida, crie uma conta de armazenamento v2 de uso geral padrão com armazenamento geo-redundante de acesso de leitura (RA-GRS) utilizando o comando [New-AzStorageAccount.](/powershell/module/az.storage/new-azstorageaccount) Lembre-se que o nome da sua conta de armazenamento deve ser único em todo o Azure, por isso substitua o valor do espaço reservado nos parênteses pelo seu próprio valor único:

```azurepowershell-interactive
New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name <account-name> `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2
```

Para permitir um espaço hierárquico para a conta de armazenamento utilizar o [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/), inclua o `-EnableHierarchicalNamespace $True` parâmetro na chamada para o comando **New-AzStorageAccount.**

A tabela a seguir mostra quais os valores a utilizar para a `-SkuName` e `-Kind` parâmetros para criar um tipo específico de conta de armazenamento com a configuração de redundância desejada.

| Tipo de conta de armazenamento | Configurações de redundância apoiadas | Valor para o parâmetro -Kind | Valores possíveis para o parâmetro -SkuName | Suporta espaço hierárquico de nomes |
|--|--|--|--|--|
| V2 de finalidade geral padrão | LRS / GRS / RA-GRS / ZRS / GZRS / RA-GZRS | StorageV2 | Standard_LRS / Standard_GRS / Standard_RAGRS/ Standard_ZRS / Standard_GZRS / Standard_RAGZRS | Yes |
| Bolhas de bloco premium | LRS / ZRS | BlockBlobStorage | Premium_LRS / Premium_ZRS | Yes |
| Ações de ficheiros premium | LRS / ZRS | Arquitorage de arquivos | Premium_LRS / Premium_ZRS | No |
| Bolhas de página premium | LRS | StorageV2 | Premium_LRS | No |
| Legado padrão de finalidade geral v1 | LRS / GRS / RA-GRS | Armazenamento | Standard_LRS / Standard_GRS / Standard_RAGRS | No |
| Armazenamento de bolhas legacy | LRS / GRS / RA-GRS | BlobStorage | Standard_LRS / Standard_GRS / Standard_RAGRS | No |

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para criar uma conta de armazenamento v2 de uso geral com a Azure CLI, primeiro criar um novo grupo de recursos chamando o [grupo az criar](/cli/azure/group#az_group_create) comando.

```azurecli-interactive
az group create \
  --name storage-resource-group \
  --location westus
```

Se não tiver a certeza de qual a região a especificar para o parâmetro `--location`, pode obter uma lista de regiões suportadas para a sua subscrição com o comando [az account list-locations](/cli/azure/account#az_account_list).

```azurecli-interactive
az account list-locations \
  --query "[].{Region:name}" \
  --out table
```

Em seguida, crie uma conta de armazenamento v2 de uso geral padrão com armazenamento geo-redundante de acesso de leitura utilizando a [conta de armazenamento az criar](/cli/azure/storage/account#az_storage_account_create) comando. Lembre-se que o nome da sua conta de armazenamento deve ser único em todo o Azure, por isso substitua o valor do espaço reservado nos parênteses pelo seu próprio valor único:

```azurecli-interactive
az storage account create \
  --name <account-name> \
  --resource-group storage-resource-group \
  --location westus \
  --sku Standard_RAGRS \
  --kind StorageV2
```

Para permitir que um espaço hierárquico para a conta de armazenamento utilize o [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/), inclua o `--enable-hierarchical-namespace true` parâmetro na chamada para a conta de armazenamento **az criar** comando. A criação de um espaço hierárquico requer a versão 2.0.79 ou posterior do Azure CLI.

A tabela a seguir mostra quais os valores a utilizar para a `-sku` e `-kind` parâmetros para criar um tipo específico de conta de armazenamento com a configuração de redundância desejada.

| Tipo de conta de armazenamento | Configurações de redundância apoiadas | Valor para o parâmetro -tipo | Valores possíveis para o parâmetro -sku | Suporta espaço hierárquico de nomes |
|--|--|--|--|--|
| V2 de finalidade geral padrão | LRS / GRS / RA-GRS / ZRS / GZRS / RA-GZRS | StorageV2 | Standard_LRS / Standard_GRS / Standard_RAGRS/ Standard_ZRS / Standard_GZRS / Standard_RAGZRS | Yes |
| Bolhas de bloco premium | LRS / ZRS | BlockBlobStorage | Premium_LRS / Premium_ZRS | Yes |
| Ações de ficheiros premium | LRS / ZRS | Arquitorage de arquivos | Premium_LRS / Premium_ZRS | No |
| Bolhas de página premium | LRS | StorageV2 | Premium_LRS | No |
| Legado padrão de finalidade geral v1 | LRS / GRS / RA-GRS | Armazenamento | Standard_LRS / Standard_GRS / Standard_RAGRS | No |
| Armazenamento de bolhas legacy | LRS / GRS / RA-GRS | BlobStorage | Standard_LRS / Standard_GRS / Standard_RAGRS | No |

# <a name="template"></a>[Modelo](#tab/template)

Pode utilizar o Azure PowerShell ou o Azure CLI para implementar um modelo de Gestor de Recursos para criar uma conta de armazenamento. O modelo utilizado neste artigo é de modelos de arranque rápido do [Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-storage-account-create/). Para executar os scripts, selecione **Experimente-o** para abrir a Azure Cloud Shell. Para colar o script, clique com o botão direito da casca e, em seguida, **selecione Pasta**.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

> [!NOTE]
> Este modelo serve apenas como um exemplo. Existem muitas configurações de conta de armazenamento que não são configuradas como parte deste modelo. Por exemplo, se pretender utilizar o [Data Lake Storage,](https://azure.microsoft.com/services/storage/data-lake-storage/)modificaria este modelo definindo a `isHnsEnabledad` propriedade do objeto para `StorageAccountPropertiesCreateParameters` `true` .

Para aprender a modificar este modelo ou criar novos, consulte:

- [Documentação do Gestor de Recursos Azure](../../azure-resource-manager/index.yml).
- [Referência do modelo de conta de armazenamento](/azure/templates/microsoft.storage/allversions).
- [Amostras adicionais do modelo de conta de armazenamento.](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Storage)

---

## <a name="delete-a-storage-account"></a>Eliminar uma conta do Storage

A eliminação de uma conta de armazenamento elimina toda a conta, incluindo todos os dados da conta, e não pode ser desfeita.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Navegue para a conta de armazenamento no [portal Azure](https://portal.azure.com).
1. Clique em **Eliminar**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para eliminar a conta de armazenamento, utilize o comando [Remove-AzStorageAccount:](/powershell/module/az.storage/remove-azstorageaccount)

```powershell
Remove-AzStorageAccount -Name <storage-account> -ResourceGroupName <resource-group>
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para eliminar a conta de armazenamento, utilize o comando de eliminação da [conta de armazenamento az:](/cli/azure/storage/account#az_storage_account_delete)

```azurecli-interactive
az storage account delete --name <storage-account> --resource-group <resource-group>
```

# <a name="template"></a>[Modelo](#tab/template)

Para eliminar a conta de armazenamento, utilize a Azure PowerShell ou a Azure CLI.

```azurepowershell-interactive
$storageResourceGroupName = Read-Host -Prompt "Enter the resource group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"
Remove-AzStorageAccount -Name $storageAccountName -ResourceGroupName $storageResourceGroupName
```

```azurecli-interactive
echo "Enter the resource group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account delete --name storageAccountName --resource-group resourceGroupName
```

---

Em alternativa, pode eliminar o grupo de recursos, que elimina a conta de armazenamento e quaisquer outros recursos nesse grupo de recursos. Para obter mais informações sobre a eliminação de um grupo de recursos, consulte [eliminar o grupo de recursos e os recursos](../../azure-resource-manager/management/delete-resource-group.md).

> [!WARNING]
> Não é possível restaurar uma conta do Storage eliminada ou obter os conteúdos que esta continha antes da eliminação. Certifique-se de que faz uma cópia de segurança de tudo o que pretende guardar antes de eliminar a conta. Isto também se aplica a quaisquer recursos na conta – depois de eliminar um blob, tabela, fila ou ficheiro, este é eliminado permanentemente.
>
> Se tentar eliminar uma conta de armazenamento associada a uma máquina virtual do Azure, poderá receber um erro informando-o de que a conta de armazenamento ainda está em utilização. Para ajudar a resolver este erro, consulte [erros de resolução de problemas quando eliminar as contas de armazenamento](/troubleshoot/azure/virtual-machines/welcome-virtual-machines).

## <a name="next-steps"></a>Passos seguintes

- [Descrição geral da conta de armazenamento](storage-account-overview.md)
- [Atualizar para uma conta de armazenamento v2 para fins gerais](storage-account-upgrade.md)
- [Mover uma conta de armazenamento para outra região](storage-account-move.md)
- [Recuperar uma conta de armazenamento eliminada](storage-account-recover.md)