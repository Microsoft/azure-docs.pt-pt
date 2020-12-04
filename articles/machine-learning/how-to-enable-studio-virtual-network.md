---
title: Ativar o estúdio Azure Machine Learning numa rede virtual
titleSuffix: Azure Machine Learning
description: Saiba como configurar o estúdio Azure Machine Learning para aceder aos dados armazenados dentro de uma rede virtual.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 10/21/2020
ms.custom: contperfq4, tracking-python
ms.openlocfilehash: a90b98e8be976da9ee2669ab3b5fed4a890f0fb2
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576633"
---
# <a name="use-azure-machine-learning-studio-in-an-azure-virtual-network"></a>Use o estúdio Azure Machine Learning numa rede virtual Azure

Neste artigo, aprende-se a usar o estúdio Azure Machine Learning numa rede virtual. O estúdio inclui funcionalidades como AutoML, o designer e a rotulagem de dados. Para utilizar estas funcionalidades numa rede virtual, deve seguir os passos deste artigo.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> - Dê ao estúdio acesso aos dados armazenados dentro de uma rede virtual.
> - Aceda ao estúdio a partir de um recurso dentro de uma rede virtual.
> - Entenda como o estúdio impacta a segurança do armazenamento.

Este artigo é parte cinco de uma série de cinco partes que o acompanha através da garantia de um fluxo de trabalho de Aprendizagem automática Azure. Recomendamos vivamente que leia as peças anteriores para configurar um ambiente de rede virtual.

Veja os outros artigos desta série:

[1. Visão geral da VNet](how-to-network-security-overview.md)  >  [2. Proteja o espaço de trabalho](how-to-secure-workspace-vnet.md)  >  [3. Proteja o ambiente de treino](how-to-secure-training-vnet.md)  >  [4. Fixe o ambiente de inferencção](how-to-secure-inferencing-vnet.md)  >  **5. Ativar a funcionalidade do estúdio**


> [!IMPORTANT]
> Se o seu espaço de trabalho estiver numa __nuvem soberana__, como o Governo Azure ou o Azure China 21Vianet, os cadernos integrados _não suportam_ o armazenamento que se encontra numa rede virtual. Em alternativa, pode utilizar os blocos de notas do Jupyter Notebook de uma instância de computação. Para mais informações, consulte os dados do Access numa secção [de cadernos De cálculo.](how-to-secure-training-vnet.md#access-data-in-a-compute-instance-notebook)


## <a name="prerequisites"></a>Pré-requisitos

+ Leia a [visão geral](how-to-network-security-overview.md) da segurança da Rede para entender cenários de rede virtuais comuns e arquitetura.

+ Uma rede virtual pré-existente e uma sub-rede para usar.

+ Um espaço de trabalho de [aprendizagem automática Azure existente com ligação privada ativada](how-to-secure-workspace-vnet.md#secure-the-workspace-with-private-endpoint).

+ Uma [conta de armazenamento Azure existente adicionou a sua rede virtual.](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-service-endpoints)

## <a name="configure-data-access-in-the-studio"></a>Configurar o acesso de dados no estúdio

Algumas das funcionalidades do estúdio são desativadas por padrão numa rede virtual. Para ree capacitar estas funcionalidades, tem de ativar a identidade gerida para contas de armazenamento que pretende utilizar no estúdio. 

As seguintes operações são desativadas por padrão numa rede virtual:

* Pré-visualizar dados no estúdio.
* Visualizar dados no designer.
* Implementar um modelo no designer[(conta de armazenamento predefinido).](#enable-managed-identity-authentication-for-default-storage-accounts)
* Submeter uma experiência AutoML[(conta de armazenamento predefinido).](#enable-managed-identity-authentication-for-default-storage-accounts)
* Inicie um projeto de rotulagem.

O estúdio suporta dados de leitura dos seguintes tipos de datastore numa rede virtual:

* Blob do Azure
* Armazenamento do Azure Data Lake Ger1
* Armazenamento do Azure Data Lake Ger2
* Base de Dados SQL do Azure

### <a name="configure-datastores-to-use-workspace-managed-identity"></a>Configure as lojas de dados para utilizar a identidade gerida pelo espaço de trabalho

Depois de adicionar uma conta de armazenamento Azure à sua rede virtual com um [ponto final](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-service-endpoints) de serviço ou ponto [final privado,](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-private-endpoints)tem de configurar a sua loja de dados para utilizar a autenticação de identidade [gerida.](../active-directory/managed-identities-azure-resources/overview.md) Ao fazê-lo, permite ao estúdio aceder aos dados na sua conta de armazenamento.

A Azure Machine Learning utiliza [datastores](concept-data.md#datastores) para se conectar às contas de armazenamento. Utilize os seguintes passos para configurar uma loja de dados para utilizar a identidade gerida:

1. No estúdio, selecione __Datastores.__

1. Para atualizar uma loja de dados existente, selecione a datastore e selecione __credenciais de Atualização__.

    Para criar uma nova loja de dados, selecione __+ Nova datastore__.

1. Nas definições da loja de dados, selecione __Sim__ para  __Utilizar identidade gerida pelo espaço de trabalho para visualização de dados e perfis no estúdio Azure Machine Learning__.

    ![Screenshot mostrando como permitir a identidade gerida do espaço de trabalho](./media/how-to-enable-studio-virtual-network/enable-managed-identity.png)

Estes passos adicionam a identidade gerida pelo espaço de trabalho como __leitor__ ao serviço de armazenamento usando o Azure RBAC. __O__ acesso ao leitor permite que o espaço de trabalho recupere as definições de firewall para garantir que os dados não saem da rede virtual. As alterações podem demorar até 10 minutos a produzir efeitos.

### <a name="enable-managed-identity-authentication-for-default-storage-accounts"></a>Ativar a autenticação de identidade gerida para contas de armazenamento padrão

Cada espaço de trabalho Azure Machine Learning vem com duas contas de armazenamento predefinidas, que são definidas quando cria o seu espaço de trabalho. O estúdio utiliza as contas de armazenamento padrão para armazenar experiências e artefactos de modelo, que são críticos para certas funcionalidades no estúdio.

A tabela a seguir descreve por que razão deve ativar a autenticação de identidade gerida para as suas contas de armazenamento padrão do espaço de trabalho.

|Conta de armazenamento  | Notas  |
|---------|---------|
|Armazenamento de bolhas padrão do espaço de trabalho| Armazena os ativos de modelo do designer. Tem de ativar a autenticação de identidade gerida nesta conta de armazenamento para implementar modelos no designer. <br> <br> Pode visualizar e executar um pipeline de design se utilizar uma loja de dados não padrão que foi configurada para usar a identidade gerida. No entanto, se tentar implementar um modelo treinado sem identidade gerida ativada na datastore padrão, a implementação falhará independentemente de quaisquer outras datas-tores em uso.|
|Loja de ficheiros padrão do espaço de trabalho| Armazena ativos de experiência AutoML. Tem de ativar a autenticação de identidade gerida nesta conta de armazenamento para submeter experiências AutoML. |


![Screenshot mostrando onde as datas-tores predefinidos podem ser encontradas](./media/how-to-enable-studio-virtual-network/default-datastores.png)


### <a name="grant-workspace-managed-identity-__reader__-access-to-storage-private-link"></a>Grant workspace gerido identidade O acesso __do leitor__ ao link privado de armazenamento

Se a sua conta de armazenamento Azure utilizar um ponto final privado, deve conceder ao **leitor** de identidade gerido pelo espaço de trabalho acesso ao link privado. Para mais informações, consulte o papel incorporado do [Leitor.](../role-based-access-control/built-in-roles.md#reader) 

Se a sua conta de armazenamento utilizar um ponto final de serviço, pode saltar este passo.

## <a name="access-the-studio-from-a-resource-inside-the-vnet"></a>Aceda ao estúdio a partir de um recurso dentro do VNet

Se estiver a aceder ao estúdio a partir de um recurso dentro de uma rede virtual (por exemplo, uma instância de computação ou uma máquina virtual), deve permitir o tráfego de saída da rede virtual para o estúdio. 

Por exemplo, se estiver a utilizar grupos de segurança de rede (NSG) para restringir o tráfego de saída, adicione uma regra a um destino de __marcação__ de serviço de __AzureFrontDoor.Frontend__.

## <a name="technical-notes-for-managed-identity"></a>Notas técnicas para identidade gerida

A utilização da identidade gerida para aceder aos serviços de armazenamento tem impacto em considerações de segurança. Esta secção descreve as alterações para cada tipo de conta de armazenamento. 

Estas considerações são únicas para o __tipo de conta de armazenamento__ a que está a aceder.

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

Depois de criar um utilizador de sql contido, conceda-lhe permissões utilizando o [comando GRANT T-SQL](/sql/t-sql/statements/grant-object-permissions-transact-sql).

### <a name="azure-machine-learning-designer-intermediate-module-output"></a>Saída do módulo intermédio do designer de aprendizagem de máquinas Azure

Pode especificar a localização de saída de qualquer módulo do designer. Utilize-o para armazenar conjuntos de dados intermédios em locais separados para fins de segurança, registo ou auditoria. Para especificar a saída:

1. Selecione o módulo cuja saída gostaria de especificar.
1. No painel de definições do módulo que aparece à direita, selecione **as definições de saída**.
1. Especifique a datastore que pretende utilizar para cada saída do módulo.
 
Certifique-se de que tem acesso às contas de armazenamento intermédio na sua rede virtual. Caso contrário, o gasoduto falhará.

Também deve [permitir a autenticação de identidade gerida](#configure-datastores-to-use-workspace-managed-identity) para contas de armazenamento intermédio para visualizar dados de saída.

## <a name="next-steps"></a>Passos seguintes

Este artigo é uma parte opcional de uma série de rede virtual de quatro partes. Veja o resto dos artigos para aprender a proteger uma rede virtual:

* [Parte 1: Visão geral da rede virtual](how-to-network-security-overview.md)
* [Parte 2: Garantir os recursos do espaço de trabalho](how-to-secure-workspace-vnet.md)
* [Parte 3: Assegurar o ambiente de treino](how-to-secure-training-vnet.md)
* [Parte 4: Assegurar o ambiente de inferencção](how-to-secure-inferencing-vnet.md)