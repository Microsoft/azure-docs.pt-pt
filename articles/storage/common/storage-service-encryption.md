---
title: Encriptação azure storage para dados em repouso
description: O Azure Storage protege os seus dados encriptando-os automaticamente antes de persistir na nuvem. Pode contar com chaves geridas pela Microsoft para a encriptação dos dados na sua conta de armazenamento, ou pode gerir a encriptação com as suas próprias chaves.
services: storage
author: tamram
ms.service: storage
ms.date: 04/10/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: c737ccf83dae0cc4b198b9cd708a55b988e6593b
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457948"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>Encriptação azure storage para dados em repouso

O Azure Storage encripta automaticamente os seus dados quando é persistido na nuvem. A encriptação Azure Storage protege os seus dados e ajuda-o a cumprir os seus compromissos de segurança organizacional e conformidade.

## <a name="about-azure-storage-encryption"></a>Sobre encriptação de armazenamento azure

Os dados em Armazenamento Azure são encriptados e desencriptados de forma transparente utilizando [encriptação AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)de 256 bits , uma das cifras de blocos mais fortes disponíveis, e é compatível com O FIPS 140-2. A encriptação azure Storage é semelhante à encriptação BitLocker no Windows.

A encriptação azure Storage está ativada para todas as contas de armazenamento, incluindo o Gestor de Recursos e contas de armazenamento clássicas. A encriptação azure Storage não pode ser desativada. Como os seus dados estão protegidos por padrão, não precisa de modificar o seu código ou aplicações para tirar partido da encriptação do Armazenamento Azure.

Os dados numa conta de armazenamento são encriptados independentemente do nível de desempenho (standard ou premium), nível de acesso (quente ou fresco) ou modelo de implementação (Gestor de Recursos Azure ou clássico). Todas as bolhas no nível de arquivo também estão encriptadas. Todas as opções de redundância do Armazenamento Azure suportam encriptação, e todos os dados nas regiões primária e secundária são encriptados quando a geo-replicação está ativada. Todos os recursos de Armazenamento Azure são encriptados, incluindo bolhas, discos, ficheiros, filas e mesas. Todos os metadados do objeto também estão encriptados. Não há custos adicionais para a encriptação do Armazenamento Azure.

Cada bloco blob, append blob ou blob de página que foi escrito para O Armazenamento Azure depois de 20 de outubro de 2017 está encriptado. As bolhas criadas antes desta data continuam a ser encriptadas por um processo de fundo. Para forçar a encriptação de uma bolha que foi criada antes de 20 de outubro de 2017, pode reescrever a bolha. Para saber como verificar o estado de encriptação de uma bolha, consulte [o estado de encriptação de uma bolha](../blobs/storage-blob-encryption-status.md).

Para obter mais informações sobre os módulos criptográficos subjacentes à encriptação do Armazenamento Azure, consulte [Cryptography API: Next Generation](https://docs.microsoft.com/windows/desktop/seccng/cng-portal).

## <a name="about-encryption-key-management"></a>Sobre a gestão da chave de encriptação

Os dados de uma nova conta de armazenamento são encriptados com chaves geridas pela Microsoft. Pode contar com chaves geridas pela Microsoft para a encriptação dos seus dados, ou pode gerir a encriptação com as suas próprias chaves. Se optar por gerir a encriptação com as suas próprias chaves, tem duas opções:

- Pode especificar uma *chave gerida pelo cliente* com o Azure Key Vault para encriptar e desencriptar dados no armazenamento blob e nos Ficheiros Azure. <sup>1,2</sup> Para obter mais informações sobre as chaves geridas pelo cliente, consulte Utilize as chaves geridas pelo cliente com o Cofre de [Chaves Azure para gerir a encriptação do Armazenamento Azure](encryption-customer-managed-keys.md).
- Pode especificar uma *chave fornecida pelo cliente* nas operações de armazenamento da Blob. Um cliente que faça um pedido de leitura ou escrita contra o armazenamento blob pode incluir uma chave de encriptação no pedido de controlo granular sobre como os dados blob são encriptados e desencriptados. Para obter mais informações sobre as chaves fornecidas pelo cliente, [consulte Forneça uma chave de encriptação num pedido de armazenamento Blob (pré-visualização)](encryption-customer-provided-keys.md).

A tabela seguinte compara as principais opções de gestão para encriptação de Armazenamento Azure.

|                                        |    Chaves geridas pela Microsoft                             |    Chaves geridas pelo cliente                                                                                                                        |    Chaves fornecidas pelo cliente                                                          |
|----------------------------------------|-------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------|
|    Operações de encriptação/desencriptação    |    Azure                                              |    Azure                                                                                                                                        |    Azure                                                                         |
|    Serviços de Armazenamento Azure suportados    |    Todos                                                |    Armazenamento blob, Ficheiros Azure<sup>1,2</sup>                                                                                                               |    Armazenamento de blobs                                                                  |
|    Armazenamento de chaves                         |    Loja chave da Microsoft    |    Azure Key Vault                                                                                                                              |    Loja-chave do cliente                                                                 |
|    Responsabilidade de rotação chave         |    Microsoft                                          |    Cliente                                                                                                                                     |    Cliente                                                                      |
|    Controlo de chaves                          |    Microsoft                                     |    Cliente                                                                                                                    |    Cliente                                                                 |

<sup>1</sup> Para obter informações sobre a criação de uma conta que suporte o uso de chaves geridas pelo cliente com armazenamento de fila, consulte [Criar uma conta que suporte as chaves geridas pelo cliente para filas](account-encryption-key-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).<br />
<sup>2</sup> Para obter informações sobre a criação de uma conta que suporte a utilização de chaves geridas pelo cliente com armazenamento de mesa, consulte [Criar uma conta que suporte as chaves geridas pelo cliente para tabelas](account-encryption-key-create.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json).

## <a name="next-steps"></a>Passos seguintes

- [O que é o cofre de chave do Azure?](../../key-vault/general/overview.md)
- [Configurar chaves geridas pelo cliente para a encriptação do Armazenamento do Microsoft Azure no portal do Azure](storage-encryption-keys-portal.md)
- [Configurar chaves geridas pelo cliente para a encriptação do Armazenamento do Microsoft Azure no PowerShell](storage-encryption-keys-powershell.md)
- [Configurar chaves geridas pelo cliente para a encriptação do Armazenamento do Microsoft Azure na CLI do Azure](storage-encryption-keys-cli.md)
