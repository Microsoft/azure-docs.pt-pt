---
title: Ativar o estúdio Azure Machine Learning numa rede virtual
titleSuffix: Azure Machine Learning
description: Utilize o estúdio Azure Machine Learning para aceder aos dados armazenados dentro de uma rede virtual.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 10/21/2020
ms.custom: contperfq4, tracking-python
ms.openlocfilehash: b6d46dfc348cc518daf2e6af4d5b9677148c3911
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92503220"
---
# <a name="use-azure-machine-learning-studio-in-an-azure-virtual-network"></a>Use o estúdio Azure Machine Learning numa rede virtual Azure

Neste artigo, aprende-se a usar o estúdio Azure Machine Learning numa rede virtual. Saiba como:

> [!div class="checklist"]
> - Aceda ao estúdio a partir de um recurso dentro de uma rede virtual.
> - Configure os pontos finais privados para contas de armazenamento.
> - Dê ao estúdio acesso aos dados armazenados dentro de uma rede virtual.
> - Entenda como o estúdio impacta a segurança do armazenamento.

Este artigo é parte cinco de uma série de cinco partes que o acompanha através da garantia de um fluxo de trabalho de Aprendizagem automática Azure. Recomendamos vivamente que leia a [primeira parte: visão geral do VNet](how-to-network-security-overview.md) para entender primeiro a arquitetura geral. 

Veja os outros artigos desta série:

[1. Visão geral da VNet](how-to-network-security-overview.md)  >  [2. Proteja o espaço de trabalho](how-to-secure-workspace-vnet.md)  >  [3. Proteja o ambiente de treino](how-to-secure-training-vnet.md)  >  [4. Fixe o ambiente de inferencção](how-to-secure-inferencing-vnet.md)  >  **5. Ativar a funcionalidade do estúdio**


> [!IMPORTANT]
> Se o seu espaço de trabalho estiver numa __nuvem soberana__, como o Governo Azure ou o Azure China 21Vianet, os cadernos integrados _não suportam_ o armazenamento que se encontra numa rede virtual. Em vez disso, pode usar os Cadernos Jupyter de uma instância computacional. Para mais informações, consulte os dados do Access numa secção [de cadernos De cálculo.](how-to-secure-training-vnet.md#access-data-in-a-compute-instance-notebook)


## <a name="prerequisites"></a>Pré-requisitos

+ Leia a [visão geral](how-to-network-security-overview.md) da segurança da Rede para entender cenários de rede virtuais comuns e arquitetura de rede virtual em geral.

+ Uma rede virtual pré-existente e uma sub-rede para usar.

+ Um espaço de trabalho de [aprendizagem automática Azure existente com ligação privada ativada](how-to-secure-workspace-vnet.md#secure-the-workspace-with-private-endpoint).

+ Uma [conta de armazenamento Azure existente adicionou a sua rede virtual.](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-service-endpoints)

## <a name="access-the-studio-from-a-resource-inside-the-vnet"></a>Aceda ao estúdio a partir de um recurso dentro do VNet

Se estiver a aceder ao estúdio a partir de um recurso dentro de uma rede virtual (por exemplo, uma instância de computação ou uma máquina virtual), deve permitir o tráfego de saída da rede virtual para o estúdio. 

Por exemplo, se estiver a utilizar grupos de segurança de rede (NSG) para restringir o tráfego de saída, adicione uma regra a um destino de __marcação__ de serviço de __AzureFrontDoor.Frontend__.

## <a name="access-data-using-the-studio"></a>Aceder a dados usando o estúdio

Depois de adicionar uma conta de armazenamento Azure à sua rede virtual com um [ponto final](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-service-endpoints) de serviço ou ponto [final privado,](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-private-endpoints)tem de configurar a sua conta de armazenamento para utilizar a [identidade gerida](../active-directory/managed-identities-azure-resources/overview.md) para permitir ao estúdio o acesso aos seus dados.

Se não ativar a identidade gerida, receberá este erro, `Error: Unable to profile this dataset. This might be because your data is stored behind a virtual network or your data does not support profile.` além disso, as seguintes operações serão desativadas:

* Pré-visualizar dados no estúdio.
* Visualizar dados no designer.
* Submeta uma experiência AutoML.
* Inicie um projeto de rotulagem.

> [!NOTE]
> [A rotulagem de dados assistidos ML](how-to-create-labeling-projects.md#use-ml-assisted-labeling) não suporta contas de armazenamento padrão protegidas por trás de uma rede virtual. Deve utilizar uma conta de armazenamento não padrão para a rotulagem de dados assistidos ML. A conta de armazenamento não padrão pode ser protegida por trás da rede virtual. 

O estúdio suporta dados de leitura dos seguintes tipos de datastore numa rede virtual:

* Blob do Azure
* Armazenamento do Azure Data Lake Ger1
* Armazenamento do Azure Data Lake Ger2
* Base de Dados SQL do Azure

### <a name="configure-datastores-to-use-managed-identity"></a>Configure as lojas de dados para utilizar a identidade gerida

A Azure Machine Learning utiliza [datastores](concept-data.md#datastores) para se conectar às contas de armazenamento. Utilize os seguintes passos para configurar as suas datastores para utilizar a identidade gerida. 

1. No estúdio, selecione __Datastores.__

1. Para criar uma nova loja de dados, selecione __+ Nova datastore__.

    Para atualizar uma loja de dados existente, selecione a datastore e selecione __credenciais de Atualização__.

1. Nas definições da datastore, selecione __Sim__ para  __permitir o serviço de aprendizagem automática Azure para aceder ao armazenamento utilizando identidade gerida pelo espaço de trabalho__.


Estes passos adicionam a identidade gerida pelo espaço de trabalho como __leitor__ ao serviço de armazenamento utilizando o controlo de acesso baseado em recursos Azure (Azure RBAC). __O__ acesso ao leitor permite que o espaço de trabalho recupere as definições de firewall e certifique-se de que os dados não saem da rede virtual.

> [!NOTE]
> Estas alterações podem demorar até 10 minutos a produzir efeitos.

## <a name="technical-notes-for-managed-identity"></a>Notas técnicas para identidade gerida

A utilização da identidade gerida para aceder aos serviços de armazenamento tem impacto em algumas considerações de segurança. Estas considerações são únicas para o tipo de conta de armazenamento a que está a aceder. Esta secção descreve as alterações para cada tipo de conta de armazenamento.

### <a name="azure-blob-storage"></a>Armazenamento de Blobs do Azure

Para __o armazenamento de Azure Blob,__ a identidade gerida pelo espaço de trabalho também é adicionada como um Leitor de [Dados Blob](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) para que possa ler dados a partir do armazenamento de bolhas.

### <a name="azure-data-lake-storage-gen2-access-control"></a>Controlo de acesso a Azure Data Lake Storage Gen2

Pode utilizar as listas de controlo de acesso ao estilo Azure RBAC e POSIX (ACLs) para controlar o acesso de dados dentro de uma rede virtual.

Para utilizar o Azure RBAC, adicione a identidade gerida pelo espaço de trabalho ao papel de Leitor de [Dados Blob.](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) Para obter mais informações, consulte [o controlo de acesso baseado em funções da Azure.](../storage/blobs/data-lake-storage-access-control-model.md#role-based-access-control)

Para utilizar ACLs, a identidade gerida pelo espaço de trabalho pode ser atribuída ao acesso como qualquer outro princípio de segurança. Para obter mais informações, consulte [as listas de controlo do Access em ficheiros e diretórios.](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)

### <a name="azure-data-lake-storage-gen1-access-control"></a>Controlo de acesso a Azure Data Lake Storage Gen1

Azure Data Lake Storage Gen1 suporta apenas listas de controlo de acesso ao estilo POSIX. Pode atribuir o acesso de identidade gerido pelo espaço de trabalho aos recursos, tal como qualquer outro princípio de segurança. Para obter mais informações, consulte [o controlo de acesso em Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md).

### <a name="azure-sql-database-contained-user"></a>Azure SQL Database continha utilizador

Para aceder aos dados armazenados numa Base de Dados Azure SQL utilizando identidade gerida, tem de criar um utilizador de sql contido que mapeia para a identidade gerida. Para obter mais informações sobre a criação de um utilizador a partir de um fornecedor externo, consulte [criar utilizadores contidos mapeados para identidades AD Azure](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities).

Depois de criar um utilizador de sql contido, conceda-lhe permissões utilizando o [comando GRANT T-SQL](https://docs.microsoft.com/sql/t-sql/statements/grant-object-permissions-transact-sql).

### <a name="azure-machine-learning-designer-default-datastore"></a>Loja de dados padrão do designer de machine learning Azure Machine Learning

O designer utiliza a conta de armazenamento anexada ao seu espaço de trabalho para armazenar a saída por padrão. No entanto, pode especi-lo para armazenar a saída em qualquer data-loja a que tenha acesso. Se o seu ambiente utilizar redes virtuais, pode utilizar estes controlos para garantir que os seus dados permanecem seguros e acessíveis.

Para definir um novo armazenamento predefinido para um oleoduto:

1. Num rascunho de pipeline, selecione o **ícone de engrenagem Definições** perto do título do seu oleoduto.
1. Selecione a **loja de dados predefinitiva Select**.
1. Especifique uma nova loja de dados.

Também pode sobrepor a datastore predefinido numa base por módulo. Isto dá-lhe controlo sobre o local de armazenamento de cada módulo individual.

1. Selecione o módulo cuja saída pretende especificar.
1. Expandir a secção **de definições de saída.**
1. Selecione **as definições de saída predefinido do Override**.
1. Selecione **definições de saída de conjunto**.
1. Especifique uma nova loja de dados.

## <a name="next-steps"></a>Passos seguintes

Este artigo é uma parte opcional de uma série de rede virtual de quatro partes. Veja o resto dos artigos para aprender a proteger uma rede virtual:

* [Parte 1: Visão geral da rede virtual](how-to-network-security-overview.md)
* [Parte 2: Garantir os recursos do espaço de trabalho](how-to-secure-workspace-vnet.md)
* [Parte 3: Assegurar o ambiente de treino](how-to-secure-training-vnet.md)
* [Parte 4: Assegurar o ambiente de inferencção](how-to-secure-inferencing-vnet.md)