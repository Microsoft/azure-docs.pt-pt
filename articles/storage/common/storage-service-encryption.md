---
title: Criptografia de armazenamento do Azure para dados em repouso
description: O armazenamento do Azure protege seus dados criptografando-os automaticamente antes de mantê-los para a nuvem. Você pode contar com chaves gerenciadas pela Microsoft para a criptografia da sua conta de armazenamento ou pode gerenciar a criptografia com suas próprias chaves.
services: storage
author: tamram
ms.service: storage
ms.date: 02/05/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 86d6a63601036abdde4ee7ae73114566d749feca
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2020
ms.locfileid: "77082827"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>Criptografia de armazenamento do Azure para dados em repouso

O armazenamento do Azure criptografa automaticamente os dados quando eles são persistidos para a nuvem. A criptografia de armazenamento do Azure protege seus dados e para ajudá-lo a atender aos compromissos de segurança e conformidade da organização.

## <a name="about-azure-storage-encryption"></a>Sobre a criptografia de armazenamento do Azure

Os dados em Armazenamento Azure são encriptados e desencriptados de forma transparente utilizando [encriptação AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)de 256 bits , uma das cifras de blocos mais fortes disponíveis, e é compatível com O FIPS 140-2. A encriptação azure Storage é semelhante à encriptação BitLocker no Windows.

A criptografia de armazenamento do Azure está habilitada para todas as novas contas de armazenamento, incluindo as contas do Resource Manager e do armazenamento clássico. A criptografia de armazenamento do Azure não pode ser desabilitada. Como os dados são protegidos por padrão, você não precisa modificar seu código ou aplicativos para tirar proveito da criptografia de armazenamento do Azure.

As contas de armazenamento são criptografadas independentemente de seu nível de desempenho (Standard ou Premium) ou modelo de implantação (Azure Resource Manager ou clássico). Todas as opções de redundância de armazenamento do Azure dão suporte à criptografia e todas as cópias de uma conta de armazenamento são criptografadas. Todos os recursos de armazenamento do Azure são criptografados, incluindo BLOBs, discos, arquivos, filas e tabelas. Todos os metadados de objeto também são criptografados.

A criptografia não afeta o desempenho do armazenamento do Azure. Não há nenhum custo adicional para a criptografia de armazenamento do Azure.

Todos os blobs de blocos, BLOB de acréscimo ou BLOB de páginas que foram gravados no armazenamento do Azure após 20 de outubro de 2017 são criptografados. Os BLOBs criados antes dessa data continuam a ser criptografados por um processo em segundo plano. Para forçar a criptografia de um blob que foi criado antes de 20 de outubro de 2017, você pode reescrever o blob. Para saber como verificar o estado de encriptação de uma bolha, consulte [o estado de encriptação de uma bolha](../blobs/storage-blob-encryption-status.md).

Para obter mais informações sobre os módulos criptográficos subjacentes à encriptação do Armazenamento Azure, consulte [Cryptography API: Next Generation](https://docs.microsoft.com/windows/desktop/seccng/cng-portal).

## <a name="about-encryption-key-management"></a>Sobre a gestão da chave de encriptação

Você pode contar com chaves gerenciadas pela Microsoft para a criptografia da sua conta de armazenamento ou pode gerenciar a criptografia com suas próprias chaves. Se você optar por gerenciar a criptografia com suas próprias chaves, terá duas opções:

- Pode especificar uma *chave gerida pelo cliente* com o Azure Key Vault para encriptar e desencriptar dados no armazenamento blob e nos Ficheiros Azure. <sup>1,2</sup>
- Pode especificar uma *chave fornecida pelo cliente* nas operações de armazenamento da Blob. Um cliente que faz uma solicitação de leitura ou gravação no armazenamento de blob pode incluir uma chave de criptografia na solicitação de controle granular sobre como os dados de blob são criptografados e descriptografados.

A tabela a seguir compara as principais opções de gerenciamento de criptografia do armazenamento do Azure.

|                                        |    Chaves gerenciadas pela Microsoft                             |    Chaves geridas pelo cliente                                                                                                                        |    Chaves fornecidas pelo cliente                                                          |
|----------------------------------------|-------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------|
|    Operações de criptografia/descriptografia    |    Azure                                              |    Azure                                                                                                                                        |    Azure                                                                         |
|    Serviços de armazenamento do Azure com suporte    |    Todos                                                |    Armazenamento blob, Ficheiros Azure<sup>1,2</sup>                                                                                                               |    Armazenamento de blobs                                                                  |
|    Armazenamento de chaves                         |    Repositório de chaves da Microsoft    |    Azure Key Vault                                                                                                                              |    Azure Key Vault ou qualquer outro repositório de chaves                                                                 |
|    Responsabilidade de rotação de chave         |    Microsoft                                          |    Cliente                                                                                                                                     |    Cliente                                                                      |
|    Utilização de chaves                           |    Microsoft                                          |    Portal do Azure, API REST do provedor de recursos de armazenamento, bibliotecas de gerenciamento de armazenamento do Azure, PowerShell, CLI        |    API REST do armazenamento do Azure (armazenamento de BLOB), bibliotecas de cliente de armazenamento do Azure    |
|    Acesso chave                          |    Somente Microsoft                                     |    Microsoft, cliente                                                                                                                    |    Somente cliente                                                                 |

<sup>1</sup> Para obter informações sobre a criação de uma conta que suporte o uso de chaves geridas pelo cliente com armazenamento de fila, consulte [Criar uma conta que suporte as chaves geridas pelo cliente para filas](account-encryption-key-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).<br />
<sup>2</sup> Para obter informações sobre a criação de uma conta que suporte a utilização de chaves geridas pelo cliente com armazenamento de mesa, consulte [Criar uma conta que suporte as chaves geridas pelo cliente para tabelas](account-encryption-key-create.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json).

As seguintes secções descrevem cada uma das opções para a gestão chave em maior detalhe.

## <a name="microsoft-managed-keys"></a>Chaves gerenciadas pela Microsoft

Por padrão, sua conta de armazenamento usa chaves de criptografia gerenciadas pela Microsoft. Pode ver as definições de encriptação da sua conta de armazenamento na secção de **encriptação** do [portal Azure,](https://portal.azure.com)como mostra a seguinte imagem.

![Exibir conta criptografada com chaves gerenciadas pela Microsoft](media/storage-service-encryption/encryption-microsoft-managed-keys.png)

## <a name="customer-managed-keys-with-azure-key-vault"></a>Chaves gerenciadas pelo cliente com Azure Key Vault

Você pode gerenciar a criptografia de armazenamento do Azure no nível da conta de armazenamento com suas próprias chaves. Quando você especifica uma chave gerenciada pelo cliente no nível da conta de armazenamento, essa chave é usada para proteger e controlar o acesso à chave de criptografia raiz da conta de armazenamento que, por sua vez, é usada para criptografar e descriptografar todos os dados de BLOB e arquivo. Chaves gerenciadas pelo cliente oferecem maior flexibilidade para criar, girar, desabilitar e revogar controles de acesso. Você também pode auditar as chaves de criptografia usadas para proteger seus dados.

Você deve usar Azure Key Vault para armazenar as chaves gerenciadas pelo cliente. Você pode criar suas próprias chaves e armazená-las em um cofre de chaves ou pode usar as APIs de Azure Key Vault para gerar chaves. A conta de armazenamento e o cofre de chaves devem estar na mesma região e no mesmo locatário Azure Active Directory (Azure AD), mas podem estar em assinaturas diferentes. Para mais informações sobre o Cofre de Chaves Azure, veja [o que é o Cofre de Chaves Azure?](../../key-vault/key-vault-overview.md)

Este diagrama mostra como o armazenamento do Azure usa Azure Active Directory e Azure Key Vault para fazer solicitações usando a chave gerenciada pelo cliente:

![Diagrama mostrando como as chaves gerenciadas pelo cliente funcionam no armazenamento do Azure](media/storage-service-encryption/encryption-customer-managed-keys-diagram.png)

A lista a seguir explica as etapas numeradas no diagrama:

1. Um administrador de Azure Key Vault concede permissões a chaves de criptografia para a identidade gerenciada associada à conta de armazenamento.
2. Um administrador de armazenamento do Azure configura a criptografia com uma chave gerenciada pelo cliente para a conta de armazenamento.
3. O armazenamento do Azure usa a identidade gerenciada associada à conta de armazenamento para autenticar o acesso a Azure Key Vault por meio de Azure Active Directory.
4. O armazenamento do Azure encapsula a chave de criptografia da conta com a chave do cliente em Azure Key Vault.
5. Para operações de leitura/gravação, o armazenamento do Azure envia solicitações para Azure Key Vault encapsular e desencapsular a chave de criptografia da conta para executar operações de criptografia e descriptografia.

### <a name="enable-customer-managed-keys-for-a-storage-account"></a>Habilitar chaves gerenciadas pelo cliente para uma conta de armazenamento

Quando você habilita a criptografia com chaves gerenciadas pelo cliente para uma conta de armazenamento, o armazenamento do Azure encapsula a chave de criptografia da conta com a chave gerenciada pelo cliente no cofre de chaves associado. A habilitação de chaves gerenciadas pelo cliente não afeta o desempenho e a conta é criptografada com a nova chave imediatamente, sem nenhum atraso de tempo.

Uma nova conta de armazenamento é sempre criptografada usando chaves gerenciadas pela Microsoft. Não é possível habilitar chaves gerenciadas pelo cliente no momento em que a conta é criada. As chaves gerenciadas pelo cliente são armazenadas em Azure Key Vault, e o cofre de chaves deve ser provisionado com políticas de acesso que concedem permissões de chave para a identidade gerenciada associada à conta de armazenamento. A identidade gerenciada está disponível somente depois que a conta de armazenamento é criada.

Quando você modifica a chave que está sendo usada para a criptografia de armazenamento do Azure habilitando ou desabilitando chaves gerenciadas pelo cliente, atualizando a versão da chave ou especificando uma chave diferente, a criptografia da chave raiz é alterada, mas os dados em sua conta de armazenamento do Azure não precisa ser criptografado novamente.

Para saber como usar as chaves gerenciadas pelo cliente com o Azure Key Vault para a criptografia de armazenamento do Azure, consulte um destes artigos:

- [Configure chaves geridas pelo cliente com cofre chave para encriptação de armazenamento Azure do portal Azure](storage-encryption-keys-portal.md)
- [Configure chaves geridas pelo cliente com key vault para encriptação de armazenamento Azure da PowerShell](storage-encryption-keys-powershell.md)
- [Configure chaves geridas pelo cliente com cofre chave para encriptação de armazenamento Azure do Azure CLI](storage-encryption-keys-cli.md)

> [!IMPORTANT]
> As chaves gerenciadas pelo cliente dependem de identidades gerenciadas para recursos do Azure, um recurso do Azure AD. Atualmente, as identidades gerenciadas não dão suporte a cenários entre diretórios. Quando você configura chaves gerenciadas pelo cliente no portal do Azure, uma identidade gerenciada é automaticamente atribuída à sua conta de armazenamento nos bastidores. Se, posteriormente, você mover a assinatura, o grupo de recursos ou a conta de armazenamento de um diretório do Azure AD para outro, a identidade gerenciada associada à conta de armazenamento não será transferida para o novo locatário, portanto, as chaves gerenciadas pelo cliente poderão deixar de funcionar. Para obter mais informações, consulte **a Transferência de uma subscrição entre diretórios da AD Azure** em [FAQs e questões conhecidas com identidades geridas para recursos Azure](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Armazenar chaves gerenciadas pelo cliente no Azure Key Vault

Para habilitar chaves gerenciadas pelo cliente em uma conta de armazenamento, você deve usar um Azure Key Vault para armazenar suas chaves. Deve ativar as propriedades **Soft Delete** e **Não purgar** as propriedades no cofre da chave.

Somente as chaves RSA de tamanho 2048 têm suporte com a criptografia de armazenamento do Azure. Para mais informações sobre as chaves, consulte **as chaves key vault** em [chaves, segredos e certificados do Cofre chave Azure.](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)

### <a name="rotate-customer-managed-keys"></a>Girar chaves gerenciadas pelo cliente

Você pode girar uma chave gerenciada pelo cliente em Azure Key Vault de acordo com suas políticas de conformidade. Quando a chave é girada, você deve atualizar a conta de armazenamento para usar o novo URI de chave. Para aprender a atualizar a conta de armazenamento para utilizar uma nova versão da chave no portal Azure, consulte a secção intitulada **Atualizar a versão chave** em Chaves [geridas pelo cliente configure para o Armazenamento Azure utilizando o portal Azure](storage-encryption-keys-portal.md).

A rotação da chave não aciona a nova criptografia dos dados na conta de armazenamento. Não há nenhuma ação adicional necessária do usuário.

### <a name="revoke-access-to-customer-managed-keys"></a>Revogar o acesso a chaves gerenciadas pelo cliente

Para revogar o acesso às chaves gerenciadas pelo cliente, use o PowerShell ou CLI do Azure. Para mais informações, consulte [o Cofre de Chaves Azure PowerShell](/powershell/module/az.keyvault//) ou o Cofre de Chaves [Azure CLI](/cli/azure/keyvault). Revogar o acesso bloqueia efetivamente o acesso a todos os dados da conta de armazenamento, uma vez que a chave de encriptação é inacessível pelo Armazenamento Azure.

### <a name="customer-managed-keys-for-azure-managed-disks-preview"></a>Chaves gerenciadas pelo cliente para Azure Managed disks (versão prévia)

As chaves gerenciadas pelo cliente também estão disponíveis para gerenciar a criptografia de Azure Managed disks (versão prévia). Chaves gerenciadas pelo cliente se comportam de maneira diferente para discos gerenciados do que para recursos de armazenamento do Azure Para obter mais informações, consulte a [encriptação lateral do Servidor do Azure gerido por discos geridos](../../virtual-machines/windows/disk-encryption.md) para a encriptação lateral do Windows ou [do Servidor de discos geridos](../../virtual-machines/linux/disk-encryption.md) pelo Azure para o Linux.

## <a name="customer-provided-keys-preview"></a>Chaves fornecidas pelo cliente (versão prévia)

Os clientes que fazem solicitações no armazenamento de BLOBs do Azure têm a opção de fornecer uma chave de criptografia em uma solicitação individual. Incluir a chave de criptografia na solicitação fornece controle granular das configurações de criptografia para operações de armazenamento de BLOBs. As chaves fornecidas pelo cliente (versão prévia) podem ser armazenadas em Azure Key Vault ou em outro repositório de chaves.

Por exemplo, que mostre como especificar uma chave fornecida pelo cliente a um pedido de armazenamento Blob, consulte Especificar uma chave fornecida pelo cliente a pedido de [armazenamento Blob com .NET](../blobs/storage-blob-customer-provided-key.md). 

### <a name="encrypting-read-and-write-operations"></a>Criptografando operações de leitura e gravação

Quando um aplicativo cliente fornece uma chave de criptografia na solicitação, o armazenamento do Azure executa criptografia e descriptografia de forma transparente ao ler e gravar dados de BLOB. O armazenamento do Azure grava um hash SHA-256 da chave de criptografia ao lado do conteúdo do blob. O hash é usado para verificar se todas as operações subsequentes no blob usam a mesma chave de criptografia. 

O armazenamento do Azure não armazena ou gerencia a chave de criptografia que o cliente envia com a solicitação. A chave é descartada com segurança assim que o processo de criptografia ou descriptografia estiver concluído.

Quando um cliente cria ou atualiza um BLOB usando uma chave fornecida pelo cliente, as solicitações de leitura e gravação subsequentes para esse blob também devem fornecer a chave. Se a chave não for fornecida em uma solicitação para um blob que já foi criptografado com uma chave fornecida pelo cliente, a solicitação falhará com o código de erro 409 (conflito).

Se o aplicativo cliente enviar uma chave de criptografia na solicitação e a conta de armazenamento também for criptografada usando uma chave gerenciada pela Microsoft ou uma chave gerenciada pelo cliente, o armazenamento do Azure usará a chave fornecida na solicitação de criptografia e descriptografia.

Para enviar a chave de criptografia como parte da solicitação, um cliente deve estabelecer uma conexão segura com o armazenamento do Azure usando HTTPS.

Cada instantâneo de blob pode ter sua própria chave de criptografia.

### <a name="request-headers-for-specifying-customer-provided-keys"></a>Cabeçalhos de solicitação para especificar chaves fornecidas pelo cliente

Para chamadas REST, os clientes podem usar os seguintes cabeçalhos para transmitir informações de chave de criptografia com segurança em uma solicitação para o armazenamento de BLOBs:

|Cabeçalho de Pedido | Descrição |
|---------------|-------------|
|`x-ms-encryption-key` |Necessário para solicitações de gravação e leitura. Um valor de chave de criptografia AES-256 codificado na base64. |
|`x-ms-encryption-key-sha256`| Necessário para solicitações de gravação e leitura. A SHA256 codificada em base64 da chave de criptografia. |
|`x-ms-encryption-algorithm` | Necessário para solicitações de gravação, opcional para solicitações de leitura. Especifica o algoritmo a ser usado ao criptografar dados usando a chave especificada. Deve ser AES256. |

A especificação de chaves de criptografia na solicitação é opcional. No entanto, se você especificar um dos cabeçalhos listados acima para uma operação de gravação, deverá especificar todos eles.

### <a name="blob-storage-operations-supporting-customer-provided-keys"></a>Operações de armazenamento de BLOBs que dão suporte a chaves fornecidas pelo cliente

As operações de armazenamento de blob a seguir dão suporte ao envio de chaves de criptografia fornecidas pelo cliente em uma solicitação:

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

### <a name="rotate-customer-provided-keys"></a>Girar chaves fornecidas pelo cliente

Para girar uma chave de criptografia passada na solicitação, baixe o blob e carregue-o novamente com a nova chave de criptografia.

> [!IMPORTANT]
> O portal do Azure não pode ser usado para ler ou gravar em um contêiner ou BLOB que é criptografado com uma chave fornecida na solicitação.
>
> Certifique-se de proteger a chave de criptografia que você fornece em uma solicitação de armazenamento de BLOBs em um repositório de chaves seguro, como Azure Key Vault. Se você tentar uma operação de gravação em um contêiner ou BLOB sem a chave de criptografia, a operação falhará e você perderá o acesso ao objeto.

## <a name="azure-storage-encryption-versus-disk-encryption"></a>Criptografia de armazenamento do Azure versus criptografia de disco

A criptografia de armazenamento do Azure criptografa os blobs de páginas que retornam discos de máquina virtual do Azure. Além disso, todos os discos de máquinas virtuais Azure, incluindo discos temporários locais, podem ser opcionalmente encriptados com encriptação de [disco azure](../../security/azure-security-disk-encryption-overview.md). A Encriptação do Disco Azure utiliza [o BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) padrão da indústria no Windows e [dM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) no Linux para fornecer soluções de encriptação baseadas no sistema operativo que estão integradas com o Azure Key Vault.

## <a name="next-steps"></a>Passos seguintes

- [O que é o Cofre chave Azure?](../../key-vault/key-vault-overview.md)
- [Configurar chaves geridas pelo cliente para a encriptação do Armazenamento do Microsoft Azure no portal do Azure](storage-encryption-keys-portal.md)
- [Configurar chaves geridas pelo cliente para a encriptação do Armazenamento do Microsoft Azure no PowerShell](storage-encryption-keys-powershell.md)
- [Configurar chaves geridas pelo cliente para a encriptação do Armazenamento do Microsoft Azure na CLI do Azure](storage-encryption-keys-cli.md)
