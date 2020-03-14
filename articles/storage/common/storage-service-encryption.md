---
title: Encriptação azure storage para dados em repouso
description: O Azure Storage protege os seus dados encriptando-os automaticamente antes de persistir na nuvem. Pode contar com as chaves geridas pela Microsoft para a encriptação da sua conta de armazenamento, ou pode gerir a encriptação com as suas próprias chaves.
services: storage
author: tamram
ms.service: storage
ms.date: 03/09/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 028b186964643a08a4370741a3f1ff2ba33a4e85
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79370309"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>Encriptação azure storage para dados em repouso

O Azure Storage encripta automaticamente os seus dados quando é persistido na nuvem. A encriptação Azure Storage protege os seus dados e ajuda-o a cumprir os seus compromissos de segurança organizacional e conformidade.

## <a name="about-azure-storage-encryption"></a>Sobre encriptação de armazenamento azure

Os dados em Armazenamento Azure são encriptados e desencriptados de forma transparente utilizando [encriptação AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)de 256 bits , uma das cifras de blocos mais fortes disponíveis, e é compatível com O FIPS 140-2. A encriptação azure Storage é semelhante à encriptação BitLocker no Windows.

A encriptação azure Storage está ativada para todas as novas contas de armazenamento, incluindo o Gestor de Recursos e contas de armazenamento clássicas. A encriptação azure Storage não pode ser desativada. Como os seus dados estão protegidos por padrão, não precisa de modificar o seu código ou aplicações para tirar partido da encriptação do Armazenamento Azure.

As contas de armazenamento são encriptadas independentemente do seu nível de desempenho (standard ou premium) ou modelo de implementação (Azure Resource Manager ou classic). Todas as opções de redundância do Armazenamento Azure suportam encriptação, e todas as cópias de uma conta de armazenamento estão encriptadas. Todos os recursos de Armazenamento Azure são encriptados, incluindo bolhas, discos, ficheiros, filas e mesas. Todos os metadados do objeto também estão encriptados.

A encriptação não afeta o desempenho do Armazenamento Azure. Não há custos adicionais para a encriptação do Armazenamento Azure.

Cada bloco blob, append blob ou blob de página que foi escrito para O Armazenamento Azure depois de 20 de outubro de 2017 está encriptado. As bolhas criadas antes desta data continuam a ser encriptadas por um processo de fundo. Para forçar a encriptação de uma bolha que foi criada antes de 20 de outubro de 2017, pode reescrever a bolha. Para saber como verificar o estado de encriptação de uma bolha, consulte [o estado de encriptação de uma bolha](../blobs/storage-blob-encryption-status.md).

Para obter mais informações sobre os módulos criptográficos subjacentes à encriptação do Armazenamento Azure, consulte [Cryptography API: Next Generation](https://docs.microsoft.com/windows/desktop/seccng/cng-portal).

## <a name="about-encryption-key-management"></a>Sobre a gestão da chave de encriptação

Pode contar com as chaves geridas pela Microsoft para a encriptação da sua conta de armazenamento, ou pode gerir a encriptação com as suas próprias chaves. Se optar por gerir a encriptação com as suas próprias chaves, tem duas opções:

- Pode especificar uma *chave gerida pelo cliente* com o Azure Key Vault para encriptar e desencriptar dados no armazenamento blob e nos Ficheiros Azure. <sup>1,2</sup>
- Pode especificar uma *chave fornecida pelo cliente* nas operações de armazenamento da Blob. Um cliente que faça um pedido de leitura ou escrita contra o armazenamento blob pode incluir uma chave de encriptação no pedido de controlo granular sobre como os dados blob são encriptados e desencriptados.

A tabela seguinte compara as principais opções de gestão para encriptação de Armazenamento Azure.

|                                        |    Chaves geridas pela Microsoft                             |    Chaves geridas pelo cliente                                                                                                                        |    Chaves fornecidas pelo cliente                                                          |
|----------------------------------------|-------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------|
|    Operações de encriptação/desencriptação    |    Azure                                              |    Azure                                                                                                                                        |    Azure                                                                         |
|    Serviços de Armazenamento Azure suportados    |    Todos                                                |    Armazenamento blob, Ficheiros Azure<sup>1,2</sup>                                                                                                               |    Armazenamento de blobs                                                                  |
|    Armazenamento de chaves                         |    Loja chave da Microsoft    |    Azure Key Vault                                                                                                                              |    Cofre de Chaves Azure ou qualquer outra loja-chave                                                                 |
|    Responsabilidade de rotação chave         |    Microsoft                                          |    Cliente                                                                                                                                     |    Cliente                                                                      |
|    Utilização de chaves                           |    Microsoft                                          |    Portal Azure, Fornecedor de Recursos de Armazenamento REST API, Bibliotecas de gestão de armazenamento Azure, PowerShell, CLI        |    Azure Storage REST API (armazenamento blob), bibliotecas de clientes de armazenamento Azure    |
|    Acesso chave                          |    Apenas microsoft                                     |    Microsoft, Cliente                                                                                                                    |    Apenas para o cliente                                                                 |

<sup>1</sup> Para obter informações sobre a criação de uma conta que suporte o uso de chaves geridas pelo cliente com armazenamento de fila, consulte [Criar uma conta que suporte as chaves geridas pelo cliente para filas](account-encryption-key-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).<br />
<sup>2</sup> Para obter informações sobre a criação de uma conta que suporte a utilização de chaves geridas pelo cliente com armazenamento de mesa, consulte [Criar uma conta que suporte as chaves geridas pelo cliente para tabelas](account-encryption-key-create.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json).

As seguintes secções descrevem cada uma das opções para a gestão chave em maior detalhe.

## <a name="microsoft-managed-keys"></a>Chaves geridas pela Microsoft

Por padrão, a sua conta de armazenamento utiliza chaves de encriptação geridas pela Microsoft. Pode ver as definições de encriptação da sua conta de armazenamento na secção de **encriptação** do [portal Azure,](https://portal.azure.com)como mostra a seguinte imagem.

![Ver conta encriptada com chaves geridas pela Microsoft](media/storage-service-encryption/encryption-microsoft-managed-keys.png)

## <a name="customer-managed-keys-with-azure-key-vault"></a>Chaves geridas pelo cliente com cofre de chaves Azure

Pode gerir a encriptação do Armazenamento Azure ao nível da conta de armazenamento com as suas próprias chaves. Quando especifica uma chave gerida pelo cliente ao nível da conta de armazenamento, essa chave é usada para proteger e controlar o acesso à chave de encriptação da raiz para a conta de armazenamento que, por sua vez, é usada para encriptar e desencriptar todos os dados blob e ficheiros. As chaves geridas pelo cliente oferecem uma maior flexibilidade para gerir os controlos de acesso. Também pode auditar as chaves de encriptação utilizadas para proteger os seus dados.

Você deve usar o Cofre chave Azure para armazenar as suas chaves geridas pelo cliente. Pode criar as suas próprias chaves e armazená-las num cofre de chaves, ou pode usar as APIs do Cofre de Chaves Azure para gerar chaves. A conta de armazenamento e o cofre chave devem estar na mesma região e no mesmo inquilino azure Ative Directory (Azure AD), mas podem estar em diferentes subscrições. Para mais informações sobre o Cofre de Chaves Azure, veja [o que é o Cofre de Chaves Azure?](../../key-vault/key-vault-overview.md)

Este diagrama mostra como o Azure Storage utiliza o Azure Ative Directory e o Azure Key Vault para fazer pedidos utilizando a chave gerida pelo cliente:

![Diagrama mostrando como as chaves geridas pelo cliente funcionam no Armazenamento Azure](media/storage-service-encryption/encryption-customer-managed-keys-diagram.png)

A lista que se segue explica os passos numerados no diagrama:

1. Um administrador da Azure Key Vault concede permissões para encriptar chaves da identidade gerida que está associada à conta de armazenamento.
2. Um administrador de armazenamento Azure configura a encriptação com uma chave gerida pelo cliente para a conta de armazenamento.
3. O Azure Storage utiliza a identidade gerida que está associada à conta de armazenamento para autenticar o acesso ao Cofre de Chaves Azure via Diretório Ativo Azure.
4. O Azure Storage envolve a chave de encriptação da conta com a chave do cliente no Cofre de Chaves Azure.
5. Para operações de leitura/escrita, o Azure Storage envia pedidos ao Azure Key Vault para embrulhar e desembrulhar a chave de encriptação da conta para realizar operações de encriptação e desencriptação.

### <a name="enable-customer-managed-keys-for-a-storage-account"></a>Ativar as chaves geridas pelo cliente para uma conta de armazenamento

Ao ativar a encriptação com chaves geridas pelo cliente para uma conta de armazenamento, o Azure Storage envolve a chave de encriptação da conta com a chave gerida pelo cliente no cofre de chaves associado. Ativar as chaves geridas pelo cliente não afeta o desempenho, e a conta é encriptada com a nova chave imediatamente, sem qualquer atraso de tempo.

Uma nova conta de armazenamento é sempre encriptada utilizando chaves geridas pela Microsoft. Não é possível ativar as chaves geridas pelo cliente no momento em que a conta é criada. As chaves geridas pelo cliente são armazenadas no Cofre de Chaves Azure, e o cofre chave deve ser aprovisionado com políticas de acesso que concedem permissões-chave à identidade gerida que está associada à conta de armazenamento. A identidade gerida só está disponível após a criação da conta de armazenamento.

Quando modifica a chave que está a ser utilizada para encriptação de Armazenamento Azure, permitindo ou desativando chaves geridas pelo cliente, atualizando a versão chave ou especificando uma chave diferente, então a encriptação da chave raiz muda, mas os dados na sua conta De armazenamento Azure não precisa ser reencriptado.

Para aprender a usar chaves geridas pelo cliente com o Cofre chave Azure para encriptação de armazenamento azure, consulte um destes artigos:

- [Configure chaves geridas pelo cliente com cofre chave para encriptação de armazenamento Azure do portal Azure](storage-encryption-keys-portal.md)
- [Configure chaves geridas pelo cliente com key vault para encriptação de armazenamento Azure da PowerShell](storage-encryption-keys-powershell.md)
- [Configure chaves geridas pelo cliente com cofre chave para encriptação de armazenamento Azure do Azure CLI](storage-encryption-keys-cli.md)

> [!IMPORTANT]
> As chaves geridas pelo cliente dependem de identidades geridas para os recursos Azure, uma característica da Azure AD. As identidades geridas não suportam atualmente cenários transversais. Quando configura as chaves geridas pelo cliente no portal Azure, uma identidade gerida é automaticamente atribuída à sua conta de armazenamento sob as capas. Se posteriormente deslocar a conta de subscrição, de recursos ou de armazenamento de um diretório Azure AD para outro, a identidade gerida associada à conta de armazenamento não é transferida para o novo inquilino, pelo que as chaves geridas pelo cliente podem deixar de funcionar. Para obter mais informações, consulte **a Transferência de uma subscrição entre diretórios da AD Azure** em [FAQs e questões conhecidas com identidades geridas para recursos Azure](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Armazenar chaves geridas pelo cliente no Cofre de Chaves Azure

Para ativar as chaves geridas pelo cliente numa conta de armazenamento, deve utilizar um Cofre de Chave Azure para armazenar as suas chaves. Deve ativar as propriedades **Soft Delete** e **Não purgar** as propriedades no cofre da chave.

Apenas as chaves RSA do tamanho 2048 são suportadas com encriptação de Armazenamento Azure. Para mais informações sobre as chaves, consulte **as chaves key vault** em [chaves, segredos e certificados do Cofre chave Azure.](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)

### <a name="rotate-customer-managed-keys"></a>Rode as chaves geridas pelo cliente

Pode rodar uma chave gerida pelo cliente no Cofre de Chaves Azure de acordo com as suas políticas de conformidade. Quando a chave estiver rodada, tem de atualizar a conta de armazenamento para utilizar a nova chave URI. Para aprender a atualizar a conta de armazenamento para utilizar uma nova versão da chave no portal Azure, consulte a secção intitulada **Atualizar a versão chave** em Chaves [geridas pelo cliente configure para o Armazenamento Azure utilizando o portal Azure](storage-encryption-keys-portal.md).

Rodar a tecla não desencadeia a reencriptação de dados na conta de armazenamento. Não são necessárias mais medidas por parte do utilizador.

### <a name="revoke-access-to-customer-managed-keys"></a>Revogar o acesso às chaves geridas pelo cliente

Pode revogar o acesso da conta de armazenamento à chave gerida pelo cliente a qualquer momento. Após a revogação do acesso às chaves geridas pelo cliente, ou depois de a chave ter sido desativada ou eliminada, os clientes não podem ligar para operações que leiam ou escrevam a uma bolha ou aos seus metadados. As tentativas de chamada de qualquer uma das seguintes operações falharão com o código de erro 403 (Proibido) para todos os utilizadores:

- [Lista Blobs,](/rest/api/storageservices/list-blobs)quando chamado com o parâmetro `include=metadata` sobre o pedido URI
- [Obter Blob](/rest/api/storageservices/get-blob)
- [Obter Propriedades Blob](/rest/api/storageservices/get-blob-properties)
- [Obter Metadados Blob](/rest/api/storageservices/get-blob-metadata)
- [Definir Metadados Blob](/rest/api/storageservices/set-blob-metadata)
- [Snapshot Blob,](/rest/api/storageservices/snapshot-blob)quando chamado com o cabeçalho de pedido `x-ms-meta-name`
- [Copy Blob](/rest/api/storageservices/copy-blob)
- [Copiar Blob de URL](/rest/api/storageservices/copy-blob-from-url)
- [Definir Camada blob](/rest/api/storageservices/set-blob-tier)
- [Bloquear](/rest/api/storageservices/put-block)
- [Colocar bloco a partir de URL](/rest/api/storageservices/put-block-from-url)
- [Bloco de apêndice](/rest/api/storageservices/append-block)
- [Bloco de apêndice de URL](/rest/api/storageservices/append-block-from-url)
- [Coloque Blob](/rest/api/storageservices/put-blob)
- [Página de colocação](/rest/api/storageservices/put-page)
- [Colocar página a partir de URL](/rest/api/storageservices/put-page-from-url)
- [Blob cópia incremental](/rest/api/storageservices/incremental-copy-blob)

Para voltar a ligar para estas operações, restaure o acesso à chave gerida pelo cliente.

Todas as operações de dados que não estejam listadas nesta secção podem prosseguir após a revogação das chaves geridas pelo cliente ou se uma chave for desativada ou eliminada.

Para revogar o acesso às chaves geridas pelo cliente, utilize o [PowerShell](storage-encryption-keys-powershell.md#revoke-customer-managed-keys) ou [o Azure CLI](storage-encryption-keys-cli.md#revoke-customer-managed-keys).

### <a name="customer-managed-keys-for-azure-managed-disks-preview"></a>Chaves geridas pelo cliente para discos geridos pelo Azure (pré-visualização)

As chaves geridas pelo cliente também estão disponíveis para gerir a encriptação dos discos geridos pelo Azure (pré-visualização). As chaves geridas pelo cliente comportam-se de forma diferente para discos geridos do que para os recursos de Armazenamento Azure. Para obter mais informações, consulte a [encriptação lateral do Servidor do Azure gerido por discos geridos](../../virtual-machines/windows/disk-encryption.md) para a encriptação lateral do Windows ou [do Servidor de discos geridos](../../virtual-machines/linux/disk-encryption.md) pelo Azure para o Linux.

## <a name="customer-provided-keys-preview"></a>Chaves fornecidas pelo cliente (pré-visualização)

Os clientes que fazem pedidos contra o armazenamento do Blob Azure têm a opção de fornecer uma chave de encriptação num pedido individual. A inclusão da chave de encriptação no pedido fornece controlo granular sobre as definições de encriptação para operações de armazenamento blob. As chaves fornecidas pelo cliente (pré-visualização) podem ser armazenadas no Cofre de Chaves Azure ou noutra loja-chave.

Por exemplo, que mostre como especificar uma chave fornecida pelo cliente a um pedido de armazenamento Blob, consulte Especificar uma chave fornecida pelo cliente a pedido de [armazenamento Blob com .NET](../blobs/storage-blob-customer-provided-key.md).

### <a name="encrypting-read-and-write-operations"></a>Encriptar as operações de leitura e escrita

Quando uma aplicação de cliente fornece uma chave de encriptação no pedido, o Azure Storage executa encriptação e desencriptação de forma transparente ao ler e escrever dados blob. O Azure Storage escreve um hash SHA-256 da chave de encriptação ao lado do conteúdo da bolha. O haxixe é usado para verificar se todas as operações subsequentes contra a bolha usam a mesma chave de encriptação.

O Azure Storage não armazena nem gere a chave de encriptação que o cliente envia com o pedido. A chave é descartada de forma segura assim que o processo de encriptação ou desencriptação estiver completo.

Quando um cliente cria ou atualiza uma bolha utilizando uma chave fornecida pelo cliente, então posteriormente ler e escrever pedidos para essa bolha também deve fornecer a chave. Se a chave não for fornecida num pedido de uma bolha que já tenha sido encriptada com uma chave fornecida pelo cliente, então o pedido falha com o código de erro 409 (Conflito).

Se a aplicação do cliente enviar uma chave de encriptação no pedido, e a conta de armazenamento também for encriptada usando uma chave gerida pela Microsoft ou uma chave gerida pelo cliente, então o Azure Storage utiliza a chave fornecida no pedido de encriptação e desencriptação.

Para enviar a chave de encriptação como parte do pedido, um cliente deve estabelecer uma ligação segura ao Armazenamento Azure usando HTTPS.

Cada imagem blob pode ter a sua própria chave de encriptação.

### <a name="request-headers-for-specifying-customer-provided-keys"></a>Pedir cabeçalhos para especificar as chaves fornecidas pelo cliente

Para chamadas REST, os clientes podem usar os seguintes cabeçalhos para passar de forma segura informações chave de encriptação num pedido ao armazenamento blob:

|Cabeçalho de Pedido | Descrição |
|---------------|-------------|
|`x-ms-encryption-key` |Necessário tanto para escrever e ler pedidos. Um valor de chave de encriptação AES-256 codificado pela Base64. |
|`x-ms-encryption-key-sha256`| Necessário tanto para escrever e ler pedidos. O SHA256 codificado pela Base64 da chave de encriptação. |
|`x-ms-encryption-algorithm` | Obrigatório para pedidos de escrita, opcional para pedidos de leitura. Especifica o algoritmo a utilizar ao encriptar dados utilizando a chave dada. Deve ser AES256. |

Especificar as chaves de encriptação no pedido é opcional. No entanto, se especificar um dos cabeçalhos acima indicados para uma operação de escrita, então deve especificar todos.

### <a name="blob-storage-operations-supporting-customer-provided-keys"></a>Operações de armazenamento blob suportando chaves fornecidas pelo cliente

O seguinte suporte de operações de armazenamento Blob envia ndo chaves de encriptação fornecidas pelo cliente a pedido:

- [Coloque Blob](/rest/api/storageservices/put-blob)
- [Lista de blocos](/rest/api/storageservices/put-block-list)
- [Bloquear](/rest/api/storageservices/put-block)
- [Colocar bloco a partir de URL](/rest/api/storageservices/put-block-from-url)
- [Página de colocação](/rest/api/storageservices/put-page)
- [Colocar página a partir de URL](/rest/api/storageservices/put-page-from-url)
- [Bloco de apêndice](/rest/api/storageservices/append-block)
- [Definir propriedades blob](/rest/api/storageservices/set-blob-properties)
- [Definir Metadados Blob](/rest/api/storageservices/set-blob-metadata)
- [Obter Blob](/rest/api/storageservices/get-blob)
- [Obter Propriedades Blob](/rest/api/storageservices/get-blob-properties)
- [Obter Metadados Blob](/rest/api/storageservices/get-blob-metadata)
- [Snapshot Blob](/rest/api/storageservices/snapshot-blob)

### <a name="rotate-customer-provided-keys"></a>Rode as chaves fornecidas pelo cliente

Para rodar uma chave de encriptação transmitida no pedido, baixe a bolha e recarregue-a com a nova chave de encriptação.

> [!IMPORTANT]
> O portal Azure não pode ser utilizado para ler ou escrever para um recipiente ou bolha que seja encriptado com uma chave fornecida no pedido.
>
> Certifique-se de proteger a chave de encriptação que fornece num pedido de armazenamento Blob numa loja de chaves segura como o Azure Key Vault. Se tentar uma operação de escrita num recipiente ou bolha sem a chave de encriptação, a operação falhará e perderá o acesso ao objeto.

## <a name="azure-storage-encryption-versus-disk-encryption"></a>Encriptação de armazenamento azure versus encriptação do disco

A encriptação azure Storage encripta as bolhas de página que apoiam discos de máquinas virtuais Azure. Além disso, todos os discos de máquinas virtuais Azure, incluindo discos temporários locais, podem ser opcionalmente encriptados com encriptação de [disco azure](../../security/azure-security-disk-encryption-overview.md). A Encriptação do Disco Azure utiliza [o BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) padrão da indústria no Windows e [dM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) no Linux para fornecer soluções de encriptação baseadas no sistema operativo que estão integradas com o Azure Key Vault.

## <a name="next-steps"></a>Passos seguintes

- [O que é o Cofre chave Azure?](../../key-vault/key-vault-overview.md)
- [Configurar chaves geridas pelo cliente para a encriptação do Armazenamento do Microsoft Azure no portal do Azure](storage-encryption-keys-portal.md)
- [Configurar chaves geridas pelo cliente para a encriptação do Armazenamento do Microsoft Azure no PowerShell](storage-encryption-keys-powershell.md)
- [Configurar chaves geridas pelo cliente para a encriptação do Armazenamento do Microsoft Azure na CLI do Azure](storage-encryption-keys-cli.md)
