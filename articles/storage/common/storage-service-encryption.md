---
title: Azure Storage encryption for data at rest (Encriptação do Armazenamento do Azure para dados inativos)
description: O Azure Storage protege os seus dados encriptando-os automaticamente antes de os persistir na nuvem. Pode confiar nas chaves geridas pela Microsoft para a encriptação dos dados na sua conta de armazenamento, ou pode gerir a encriptação com as suas próprias chaves.
services: storage
author: tamram
ms.service: storage
ms.date: 06/17/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 8b4236e40e8dfbe6ce67bca007be0b6737a6e0c8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84945584"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>Azure Storage encryption for data at rest (Encriptação do Armazenamento do Azure para dados inativos)

O Azure Storage encripta automaticamente os seus dados quando estes são persistidos na nuvem. A encriptação do Azure Storage protege os seus dados e ajuda-o a cumprir os seus compromissos de segurança organizacional e de conformidade.

## <a name="about-azure-storage-encryption"></a>Sobre encriptação de armazenamento Azure

Os dados em Azure Storage são encriptados e desencriptados de forma transparente utilizando [encriptação AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)de 256 bits , uma das cifras de blocos mais fortes disponíveis, e é compatível com FIPS 140-2. A encriptação de armazenamento Azure é semelhante à encriptação BitLocker no Windows.

A encriptação de armazenamento Azure está ativada para todas as contas de armazenamento, incluindo gestor de recursos e contas de armazenamento clássicas. A encriptação de armazenamento Azure não pode ser desativada. Como os seus dados são protegidos por padrão, não precisa de modificar o seu código ou aplicações para tirar partido da encriptação do Azure Storage.

Os dados numa conta de armazenamento são encriptados independentemente do nível de desempenho (standard ou premium), do nível de acesso (quente ou fresco) ou do modelo de implementação (Azure Resource Manager ou clássico). Todas as bolhas no nível de arquivo também estão encriptadas. Todas as opções de redundância de armazenamento Azure suportam encriptação, e todos os dados nas regiões primária e secundária são encriptados quando a geo-replicação está ativada. Todos os recursos de Armazenamento Azure são encriptados, incluindo bolhas, discos, ficheiros, filas e tabelas. Todos os metadados de objetos também estão encriptados. Não há custo adicional para a encriptação do Armazenamento Azure.

Cada blob de bloco, blob de apêndice ou bolha de página que foi escrito para Azure Storage depois de 20 de outubro de 2017 é encriptado. As manchas criadas antes desta data continuam a ser encriptadas por um processo de fundo. Para forçar a encriptação de uma bolha que foi criada antes de 20 de outubro de 2017, você pode reescrever a bolha. Para saber como verificar o estado de encriptação de uma bolha, consulte [verifique o estado de encriptação de uma bolha](../blobs/storage-blob-encryption-status.md).

Para obter mais informações sobre os módulos criptográficos subjacentes à encriptação do Armazenamento Azure, consulte [Cryptography API: Next Generation](https://docs.microsoft.com/windows/desktop/seccng/cng-portal).

## <a name="about-encryption-key-management"></a>Sobre a gestão de chaves de encriptação

Os dados de uma nova conta de armazenamento são encriptados com as chaves geridas pela Microsoft. Pode confiar nas teclas geridas pela Microsoft para a encriptação dos seus dados, ou pode gerir a encriptação com as suas próprias chaves. Se optar por gerir a encriptação com as suas próprias chaves, tem duas opções:

- Pode especificar uma *chave gerida pelo cliente* com o Azure Key Vault para usar para encriptar e desencriptar dados no armazenamento blob e nos Ficheiros Azure. <sup>1,2</sup> Para obter mais informações sobre as chaves geridas pelo cliente, consulte [utilize as teclas geridas pelo cliente com o Cofre da Chave Azure para gerir a encriptação do Armazenamento Azure](encryption-customer-managed-keys.md).
- Pode especificar uma *chave fornecida pelo cliente* nas operações de armazenamento Blob. Um cliente que faça um pedido de leitura ou escrita contra o armazenamento blob pode incluir uma chave de encriptação no pedido de controlo granular sobre como os dados blob são encriptados e desencriptados. Para obter mais informações sobre as chaves fornecidas pelo cliente, consulte fornecer uma chave de [encriptação sobre um pedido de armazenamento Blob (pré-visualização)](encryption-customer-provided-keys.md).

A tabela seguinte compara as opções de gestão chave para a encriptação do Azure Storage.

|                                        |    Chaves geridas pela Microsoft                             |    Chaves geridas pelo cliente                                                                                                                        |    Chaves fornecidas pelo cliente                                                          |
|----------------------------------------|-------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------|
|    Operações de encriptação/desencriptação    |    Azure                                              |    Azure                                                                                                                                        |    Azure                                                                         |
|    Serviços de Armazenamento Azure apoiados    |    Todos                                                |    Armazenamento blob, Ficheiros Azure<sup>1,2</sup>                                                                                                               |    Armazenamento de blobs                                                                  |
|    Armazenamento de chaves                         |    Loja de chaves da Microsoft    |    Azure Key Vault                                                                                                                              |    Loja chave do próprio cliente                                                                 |
|    Responsabilidade de rotação chave         |    Microsoft                                          |    Cliente                                                                                                                                     |    Cliente                                                                      |
|    Controlo de chaves                          |    Microsoft                                     |    Cliente                                                                                                                    |    Cliente                                                                 |

<sup>1</sup> Para obter informações sobre a criação de uma conta que suporte a utilização de chaves geridas pelo cliente com armazenamento de fila, consulte [Criar uma conta que suporte chaves geridas pelo cliente para filas](account-encryption-key-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).<br />
<sup>2</sup> Para obter informações sobre a criação de uma conta que suporte a utilização de chaves geridas pelo cliente com armazenamento de mesa, consulte [Criar uma conta que suporte chaves geridas pelo cliente para tabelas](account-encryption-key-create.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json).

Para obter informações sobre encriptação e gestão de chaves para discos geridos pelo Azure, consulte [a encriptação do lado do Servidor dos discos geridos pelo Azure](../../virtual-machines/windows/disk-encryption.md) para VMs do Windows ou [encriptação do lado do Servidor de discos geridos pelo Azure](../../virtual-machines/linux/disk-encryption.md) para Os VMs Do Linux.

## <a name="next-steps"></a>Próximos passos

- [O que é o cofre de chave do Azure?](../../key-vault/general/overview.md)
- [Configurar chaves geridas pelo cliente para a encriptação do Armazenamento do Microsoft Azure no portal do Azure](storage-encryption-keys-portal.md)
- [Configurar chaves geridas pelo cliente para a encriptação do Armazenamento do Microsoft Azure no PowerShell](storage-encryption-keys-powershell.md)
- [Configurar chaves geridas pelo cliente para a encriptação do Armazenamento do Microsoft Azure na CLI do Azure](storage-encryption-keys-cli.md)
