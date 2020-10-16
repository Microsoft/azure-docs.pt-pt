---
title: Azure Storage encryption for data at rest (Encriptação do Armazenamento do Azure para dados inativos)
description: O Azure Storage protege os seus dados encriptando-os automaticamente antes de os persistir na nuvem. Pode confiar nas chaves geridas pela Microsoft para a encriptação dos dados na sua conta de armazenamento, ou pode gerir a encriptação com as suas próprias chaves.
services: storage
author: tamram
ms.service: storage
ms.date: 09/17/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 19f0027b506b78ef81f9acc25a94ef9ab74643e2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90985755"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>Azure Storage encryption for data at rest (Encriptação do Armazenamento do Azure para dados inativos)

O Azure Storage encripta automaticamente os seus dados quando estes são persistidos na nuvem. A encriptação do Azure Storage protege os seus dados e ajuda-o a cumprir os seus compromissos de segurança organizacional e de conformidade.

## <a name="about-azure-storage-encryption"></a>Sobre encriptação de armazenamento Azure

Os dados em Azure Storage são encriptados e desencriptados de forma transparente utilizando [encriptação AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)de 256 bits , uma das cifras de blocos mais fortes disponíveis, e é compatível com FIPS 140-2. A encriptação de armazenamento Azure é semelhante à encriptação BitLocker no Windows.

A encriptação de armazenamento Azure está ativada para todas as contas de armazenamento, incluindo gestor de recursos e contas de armazenamento clássicas. A encriptação de armazenamento Azure não pode ser desativada. Como os seus dados são protegidos por padrão, não precisa de modificar o seu código ou aplicações para tirar partido da encriptação do Azure Storage.

Os dados numa conta de armazenamento são encriptados independentemente do nível de desempenho (standard ou premium), do nível de acesso (quente ou fresco) ou do modelo de implementação (Azure Resource Manager ou clássico). Todas as bolhas no nível de arquivo também estão encriptadas. Todas as opções de redundância de armazenamento Azure suportam encriptação, e todos os dados nas regiões primária e secundária são encriptados quando a geo-replicação está ativada. Todos os recursos de Armazenamento Azure são encriptados, incluindo bolhas, discos, ficheiros, filas e tabelas. Todos os metadados de objetos também estão encriptados. Não há custo adicional para a encriptação do Armazenamento Azure.

Cada blob de bloco, blob de apêndice ou bolha de página que foi escrito para Azure Storage depois de 20 de outubro de 2017 é encriptado. As manchas criadas antes desta data continuam a ser encriptadas por um processo de fundo. Para forçar a encriptação de uma bolha que foi criada antes de 20 de outubro de 2017, você pode reescrever a bolha. Para saber como verificar o estado de encriptação de uma bolha, consulte [verifique o estado de encriptação de uma bolha](../blobs/storage-blob-encryption-status.md).

Para obter mais informações sobre os módulos criptográficos subjacentes à encriptação do Armazenamento Azure, consulte [Cryptography API: Next Generation](https://docs.microsoft.com/windows/desktop/seccng/cng-portal).

Para obter informações sobre encriptação e gestão de chaves para discos geridos pelo Azure, consulte [a encriptação do lado do Servidor dos discos geridos pelo Azure](../../virtual-machines/windows/disk-encryption.md) para VMs do Windows ou [encriptação do lado do Servidor de discos geridos pelo Azure](../../virtual-machines/linux/disk-encryption.md) para Os VMs Do Linux.

## <a name="about-encryption-key-management"></a>Sobre a gestão de chaves de encriptação

Os dados de uma nova conta de armazenamento são encriptados com as chaves geridas pela Microsoft por padrão. Pode continuar a confiar nas chaves geridas pela Microsoft para a encriptação dos seus dados, ou pode gerir a encriptação com as suas próprias chaves. Se optar por gerir a encriptação com as suas próprias chaves, tem duas opções. Pode utilizar qualquer tipo de gestão de chaves, ou ambos:

- Pode especificar uma *chave gerida pelo cliente* para encriptar e desencriptar dados no armazenamento blob e nos Ficheiros Azure. <sup>1,2</sup> As chaves geridas pelo cliente devem ser armazenadas no Cofre da Chave Azure ou no Modelo de Segurança Gerido do Cofre do Cofre de Azure Key (HSM) (pré-visualização). Para obter mais informações sobre as teclas geridas pelo cliente, consulte [as chaves geridas pelo cliente para encriptação de armazenamento Azure](encryption-customer-managed-keys.md).
- Pode especificar uma *chave fornecida pelo cliente* nas operações de armazenamento Blob. Um cliente que faça um pedido de leitura ou escrita contra o armazenamento blob pode incluir uma chave de encriptação no pedido de controlo granular sobre como os dados blob são encriptados e desencriptados. Para obter mais informações sobre as chaves fornecidas pelo cliente, consulte fornecer uma chave de [encriptação sobre um pedido de armazenamento Blob](../blobs/encryption-customer-provided-keys.md).

A tabela seguinte compara as opções de gestão chave para a encriptação do Azure Storage.

| Parâmetro de gestão chave | Chaves geridas pela Microsoft | Chaves geridas pelo cliente | Chaves fornecidas pelo cliente |
|--|--|--|--|
| Operações de encriptação/desencriptação | Azure | Azure | Azure |
| Serviços de Armazenamento Azure apoiados | Todos | Armazenamento blob, Ficheiros Azure<sup>1,2</sup> | Armazenamento de blobs |
| Armazenamento de chaves | Loja de chaves da Microsoft | Cofre de chave Azure ou Cofre de Chaves HSM | Loja chave do próprio cliente |
| Responsabilidade de rotação chave | Microsoft | Cliente | Cliente |
| Controlo de chaves | Microsoft | Cliente | Cliente |

<sup>1</sup> Para obter informações sobre a criação de uma conta que suporte a utilização de chaves geridas pelo cliente com armazenamento de fila, consulte [Criar uma conta que suporte chaves geridas pelo cliente para filas](account-encryption-key-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).<br />
<sup>2</sup> Para obter informações sobre a criação de uma conta que suporte a utilização de chaves geridas pelo cliente com armazenamento de mesa, consulte [Criar uma conta que suporte chaves geridas pelo cliente para tabelas](account-encryption-key-create.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json).

> [!NOTE]
> As teclas geridas pela Microsoft são giradas adequadamente de acordo com os requisitos de conformidade. Se tiver requisitos específicos de rotação de chaves, a Microsoft recomenda que se mude para as chaves geridas pelo cliente para que possa gerir e auditar a rotação por si mesmo.

## <a name="doubly-encrypt-data-with-infrastructure-encryption"></a>Criptografe duplamente os dados com encriptação da infraestrutura

Os clientes que necessitam de elevados níveis de garantia de que os seus dados são seguros também podem permitir uma encriptação AES de 256 bits ao nível da infraestrutura de armazenamento Azure. Quando a encriptação da infraestrutura é ativada, os dados numa conta de armazenamento são encriptados duas vezes &mdash; ao nível do serviço e uma ao nível da infraestrutura &mdash; com dois algoritmos de encriptação diferentes e duas teclas diferentes. A dupla encriptação dos dados do Azure Storage protege contra um cenário em que um dos algoritmos ou chaves de encriptação pode estar comprometido. Neste cenário, a camada adicional de encriptação continua a proteger os seus dados.

A encriptação de nível de serviço suporta a utilização de chaves geridas pela Microsoft ou chaves geridas pelo cliente com o Azure Key Vault. A encriptação ao nível da infraestrutura baseia-se em chaves geridas pela Microsoft e utiliza sempre uma chave separada.

Para obter mais informações sobre como criar uma conta de armazenamento que permita encriptação de infraestrutura, consulte [Criar uma conta de armazenamento com encriptação de infraestrutura ativada para dupla encriptação de dados](infrastructure-encryption-enable.md).

## <a name="encryption-scopes-for-blob-storage-preview"></a>Âmbitos de encriptação para armazenamento blob (pré-visualização)

Por padrão, uma conta de armazenamento é encriptada com uma chave que é telescópio na conta de armazenamento. Pode optar por utilizar as teclas geridas pela Microsoft ou as teclas geridas pelo cliente armazenadas no Cofre da Chave Azure para proteger e controlar o acesso à chave que encripta os seus dados.

Os âmbitos de encriptação permitem-lhe gerir opcionalmente a encriptação ao nível do recipiente ou de uma bolha individual. Pode utilizar âmbitos de encriptação para criar limites seguros entre dados que residem na mesma conta de armazenamento, mas que pertencem a diferentes clientes.

Pode criar um ou mais âmbitos de encriptação para uma conta de armazenamento utilizando o fornecedor de recursos de armazenamento Azure. Quando cria um âmbito de encriptação, especifica se o âmbito está protegido com uma chave gerida pela Microsoft ou com uma chave gerida pelo cliente que é armazenada no Cofre da Chave Azure. Diferentes âmbitos de encriptação na mesma conta de armazenamento podem utilizar chaves geridas pela Microsoft ou geridas pelo cliente.

Depois de ter criado um âmbito de encriptação, pode especificar esse âmbito de encriptação num pedido de criação de um recipiente ou de uma bolha. Para obter mais informações sobre como criar um âmbito de encriptação, consulte [Criar e gerir os âmbitos de encriptação (pré-visualização)](../blobs/encryption-scope-manage.md).

> [!NOTE]
> Os âmbitos de encriptação não são suportados com armazenamento geo-redundante de acesso à leitura (RA-GRS) e contas de armazenamento redundante de zona de acesso à leitura (RA-GZRS) durante a pré-visualização.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

> [!IMPORTANT]
> A pré-visualização dos âmbitos de encriptação destina-se apenas à utilização não-produção. Os contratos de serviços de produção (SLAs) não estão atualmente disponíveis.
>
> Para evitar custos inesperados, certifique-se de desativar quaisquer âmbitos de encriptação que não necessita atualmente.

### <a name="create-a-container-or-blob-with-an-encryption-scope"></a>Criar um recipiente ou bolha com um âmbito de encriptação

As bolhas criadas sob um âmbito de encriptação são encriptadas com a chave especificada para esse âmbito. Pode especificar um âmbito de encriptação para uma bolha individual quando criar a bolha, ou pode especificar um âmbito de encriptação padrão quando criar um recipiente. Quando um âmbito de encriptação predefinido é especificado ao nível de um recipiente, todas as bolhas nesse recipiente são encriptadas com a chave associada ao âmbito padrão.

Quando cria uma bolha num recipiente que tenha um âmbito de encriptação padrão, pode especificar um âmbito de encriptação que substitui o âmbito de encriptação padrão se o recipiente estiver configurado para permitir substituições do âmbito de encriptação padrão. Para evitar sobreposições do âmbito de encriptação padrão, configuure o recipiente para negar sobreposições para uma bolha individual.

A leitura das operações numa bolha que pertence a um âmbito de encriptação acontece de forma transparente, desde que o âmbito de encriptação não seja desativado.

### <a name="disable-an-encryption-scope"></a>Desativar um âmbito de encriptação

Quando desativar um âmbito de encriptação, quaisquer operações de leitura ou escrita subsequentes efetuadas com o âmbito de encriptação falharão com o código de erro HTTP 403 (Proibido). Se voltar a ativar o âmbito de encriptação, as operações de leitura e escrita voltarão a proceder normalmente.

Quando um âmbito de encriptação é desativado, já não é cobrado por isso. Desative quaisquer âmbitos de encriptação que não sejam necessários para evitar encargos desnecessários.

Se o seu âmbito de encriptação estiver protegido com chaves geridas pelo cliente para o Cofre da Chave Azure, também pode eliminar a chave associada no cofre de chaves para desativar o âmbito de encriptação. Tenha em mente que as teclas geridas pelo cliente no Cofre da Chave Azure estão protegidas por proteção de eliminação e purga suave, e uma chave eliminada está sujeita ao comportamento definido por essas propriedades. Para mais informações, consulte um dos seguintes tópicos na documentação do Cofre chave Azure:

- [Como utilizar soft-delete com PowerShell](../../key-vault/general/soft-delete-powershell.md)
- [Como usar soft-delete com CLI](../../key-vault/general/soft-delete-cli.md)

> [!NOTE]
> Não é possível eliminar um âmbito de encriptação.

## <a name="next-steps"></a>Passos seguintes

- [O que é o Azure Key Vault?](../../key-vault/general/overview.md)
- [Chaves geridas pelo cliente para encriptação de armazenamento Azure](customer-managed-keys-overview.md)
- [Âmbitos de encriptação para armazenamento blob (pré-visualização)](../blobs/encryption-scope-overview.md)
