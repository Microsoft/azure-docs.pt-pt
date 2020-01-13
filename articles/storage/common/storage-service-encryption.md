---
title: Criptografia de armazenamento do Azure para dados em repouso
description: O armazenamento do Azure protege seus dados criptografando-os automaticamente antes de mantê-los para a nuvem. Você pode contar com chaves gerenciadas pela Microsoft para a criptografia da sua conta de armazenamento ou pode gerenciar a criptografia com suas próprias chaves.
services: storage
author: tamram
ms.service: storage
ms.date: 01/10/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: b74943ce3e3e67855a07fa32f15612bbb2351170
ms.sourcegitcommit: e9776e6574c0819296f28b43c9647aa749d1f5a6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/13/2020
ms.locfileid: "75913100"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>Criptografia de armazenamento do Azure para dados em repouso

O armazenamento do Azure criptografa automaticamente os dados quando eles são persistidos para a nuvem. A criptografia de armazenamento do Azure protege seus dados e para ajudá-lo a atender aos compromissos de segurança e conformidade da organização.

## <a name="about-azure-storage-encryption"></a>Sobre a criptografia de armazenamento do Azure

Os dados no armazenamento do Azure são criptografados e descriptografados de forma transparente usando a [criptografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)de 256 bits, uma das codificações de bloco mais fortes disponíveis e é compatível com o FIPS 140-2. A criptografia de armazenamento do Azure é semelhante à criptografia BitLocker no Windows.

A criptografia de armazenamento do Azure está habilitada para todas as novas contas de armazenamento, incluindo as contas do Resource Manager e do armazenamento clássico. A criptografia de armazenamento do Azure não pode ser desabilitada. Como os dados são protegidos por padrão, você não precisa modificar seu código ou aplicativos para tirar proveito da criptografia de armazenamento do Azure.

As contas de armazenamento são criptografadas independentemente de seu nível de desempenho (Standard ou Premium) ou modelo de implantação (Azure Resource Manager ou clássico). Todas as opções de redundância de armazenamento do Azure dão suporte à criptografia e todas as cópias de uma conta de armazenamento são criptografadas. Todos os recursos de armazenamento do Azure são criptografados, incluindo BLOBs, discos, arquivos, filas e tabelas. Todos os metadados de objeto também são criptografados.

A criptografia não afeta o desempenho do armazenamento do Azure. Não há nenhum custo adicional para a criptografia de armazenamento do Azure.

Todos os blobs de blocos, BLOB de acréscimo ou BLOB de páginas que foram gravados no armazenamento do Azure após 20 de outubro de 2017 são criptografados. Os BLOBs criados antes dessa data continuam a ser criptografados por um processo em segundo plano. Para forçar a criptografia de um blob que foi criado antes de 20 de outubro de 2017, você pode reescrever o blob. Para saber como verificar o status de criptografia de um blob, consulte [verificar o status de criptografia de um blob](../blobs/storage-blob-encryption-status.md).

Para obter mais informações sobre os módulos de criptografia subjacentes à criptografia de armazenamento do Azure, consulte [API de criptografia: próxima geração](https://docs.microsoft.com/windows/desktop/seccng/cng-portal).

## <a name="about-encryption-key-management"></a>Sobre o gerenciamento de chaves de criptografia

Você pode contar com chaves gerenciadas pela Microsoft para a criptografia da sua conta de armazenamento ou pode gerenciar a criptografia com suas próprias chaves. Se você optar por gerenciar a criptografia com suas próprias chaves, terá duas opções:

- Você pode especificar uma *chave gerenciada pelo cliente* com Azure Key Vault a ser usada para criptografar e descriptografar dados no armazenamento de BLOBs e em arquivos do Azure. <sup>1, 2</sup>
- Você pode especificar uma *chave fornecida pelo cliente* em operações de armazenamento de BLOBs. Um cliente que faz uma solicitação de leitura ou gravação no armazenamento de blob pode incluir uma chave de criptografia na solicitação de controle granular sobre como os dados de blob são criptografados e descriptografados.

A tabela a seguir compara as principais opções de gerenciamento de criptografia do armazenamento do Azure.

|                                        |    Chaves gerenciadas pela Microsoft                             |    Chaves gerenciadas pelo cliente                                                                                                                        |    Chaves fornecidas pelo cliente                                                          |
|----------------------------------------|-------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------|
|    Operações de criptografia/descriptografia    |    Azure                                              |    Azure                                                                                                                                        |    Azure                                                                         |
|    Serviços de armazenamento do Azure com suporte    |    Tudo                                                |    Armazenamento de BLOBs, arquivos do Azure<sup>1, 2</sup>                                                                                                               |    Armazenamento de blobs                                                                  |
|    Armazenamento de chaves                         |    Repositório de chaves da Microsoft    |    Azure Key Vault                                                                                                                              |    Azure Key Vault ou qualquer outro repositório de chaves                                                                 |
|    Responsabilidade de rotação de chave         |    Microsoft                                          |    Cliente                                                                                                                                     |    Cliente                                                                      |
|    Utilização de chaves                           |    Microsoft                                          |    Portal do Azure, API REST do provedor de recursos de armazenamento, bibliotecas de gerenciamento de armazenamento do Azure, PowerShell, CLI        |    API REST do armazenamento do Azure (armazenamento de BLOB), bibliotecas de cliente de armazenamento do Azure    |
|    Acesso à chave                          |    Somente Microsoft                                     |    Microsoft, cliente                                                                                                                    |    Somente cliente                                                                 |

<sup>1</sup> para obter informações sobre como criar uma conta que ofereça suporte ao uso de chaves gerenciadas pelo cliente com o armazenamento de filas, consulte [criar uma conta que dê suporte a chaves gerenciadas pelo cliente para filas](account-encryption-key-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).<br />
<sup>2</sup> para obter informações sobre como criar uma conta que ofereça suporte ao uso de chaves gerenciadas pelo cliente com o armazenamento de tabelas, consulte [criar uma conta que dê suporte a chaves gerenciadas pelo cliente para tabelas](account-encryption-key-create.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json).

As seções a seguir descrevem cada uma das opções de gerenciamento de chaves mais detalhadamente.

## <a name="microsoft-managed-keys"></a>Chaves gerenciadas pela Microsoft

Por padrão, sua conta de armazenamento usa chaves de criptografia gerenciadas pela Microsoft. Você pode ver as configurações de criptografia para sua conta de armazenamento na seção **criptografia** do [portal do Azure](https://portal.azure.com), conforme mostrado na imagem a seguir.

![Exibir conta criptografada com chaves gerenciadas pela Microsoft](media/storage-service-encryption/encryption-microsoft-managed-keys.png)

## <a name="customer-managed-keys-with-azure-key-vault"></a>Chaves gerenciadas pelo cliente com Azure Key Vault

Você pode gerenciar a criptografia de armazenamento do Azure no nível da conta de armazenamento com suas próprias chaves. Quando você especifica uma chave gerenciada pelo cliente no nível da conta de armazenamento, essa chave é usada para proteger e controlar o acesso à chave de criptografia raiz da conta de armazenamento que, por sua vez, é usada para criptografar e descriptografar todos os dados de BLOB e arquivo. Chaves gerenciadas pelo cliente oferecem maior flexibilidade para criar, girar, desabilitar e revogar controles de acesso. Você também pode auditar as chaves de criptografia usadas para proteger seus dados.

Você deve usar Azure Key Vault para armazenar as chaves gerenciadas pelo cliente. Você pode criar suas próprias chaves e armazená-las em um cofre de chaves ou pode usar as APIs de Azure Key Vault para gerar chaves. A conta de armazenamento e o cofre de chaves devem estar na mesma região, mas podem estar em assinaturas diferentes. Para obter mais informações sobre Azure Key Vault, consulte [o que é Azure Key Vault?](../../key-vault/key-vault-overview.md).

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

- [Configurar chaves gerenciadas pelo cliente com o Key Vault para a criptografia de armazenamento do Azure do portal do Azure](storage-encryption-keys-portal.md)
- [Configurar chaves gerenciadas pelo cliente com o Key Vault para a criptografia de armazenamento do Azure do PowerShell](storage-encryption-keys-powershell.md)
- [Configurar chaves gerenciadas pelo cliente com o Key Vault para criptografia de armazenamento do Azure do CLI do Azure](storage-encryption-keys-cli.md)

> [!IMPORTANT]
> As chaves gerenciadas pelo cliente dependem de identidades gerenciadas para recursos do Azure, um recurso do Azure Active Directory (Azure AD). Quando você configura chaves gerenciadas pelo cliente no portal do Azure, uma identidade gerenciada é automaticamente atribuída à sua conta de armazenamento nos bastidores. Se, posteriormente, você mover a assinatura, o grupo de recursos ou a conta de armazenamento de um diretório do Azure AD para outro, a identidade gerenciada associada à conta de armazenamento não será transferida para o novo locatário, portanto, as chaves gerenciadas pelo cliente poderão deixar de funcionar. Para obter mais informações, consulte **transferindo uma assinatura entre diretórios do Azure ad** em [perguntas frequentes e problemas conhecidos com identidades gerenciadas para recursos do Azure](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Armazenar chaves gerenciadas pelo cliente no Azure Key Vault

Para habilitar chaves gerenciadas pelo cliente em uma conta de armazenamento, você deve usar um Azure Key Vault para armazenar suas chaves. Você deve habilitar a **exclusão reversível** e **não limpar** as propriedades no cofre de chaves.

Somente as chaves RSA de tamanho 2048 têm suporte com a criptografia de armazenamento do Azure. Para obter mais informações sobre chaves, consulte **Key Vault chaves** em [sobre Azure Key Vault chaves, segredos e certificados](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).

O cofre de chaves deve estar localizado na mesma assinatura que a conta de armazenamento. O armazenamento do Azure usa identidades gerenciadas para recursos do Azure para autenticar no cofre de chaves para operações de criptografia e descriptografia. Atualmente, as identidades gerenciadas não dão suporte a cenários entre diretórios.

### <a name="rotate-customer-managed-keys"></a>Girar chaves gerenciadas pelo cliente

Você pode girar uma chave gerenciada pelo cliente em Azure Key Vault de acordo com suas políticas de conformidade. Quando a chave é girada, você deve atualizar a conta de armazenamento para usar o novo URI de chave. Para saber como atualizar a conta de armazenamento para usar uma nova versão da chave no portal do Azure, consulte a seção intitulada **atualizar a versão da chave** em [Configurar chaves gerenciadas pelo cliente para o armazenamento do Azure usando o portal do Azure](storage-encryption-keys-portal.md).

A rotação da chave não aciona a nova criptografia dos dados na conta de armazenamento. Não há nenhuma ação adicional necessária do usuário.

### <a name="revoke-access-to-customer-managed-keys"></a>Revogar o acesso a chaves gerenciadas pelo cliente

Para revogar o acesso às chaves gerenciadas pelo cliente, use o PowerShell ou CLI do Azure. Para obter mais informações, consulte [Azure Key Vault PowerShell](/powershell/module/az.keyvault//) ou [Azure Key Vault CLI](/cli/azure/keyvault). Revogar o acesso efetivamente bloqueia o acesso a todos os dados na conta de armazenamento, pois a chave de criptografia é inacessível pelo armazenamento do Azure.

### <a name="customer-managed-keys-for-azure-managed-disks-preview"></a>Chaves gerenciadas pelo cliente para Azure Managed disks (versão prévia)

As chaves gerenciadas pelo cliente também estão disponíveis para gerenciar a criptografia de Azure Managed disks (versão prévia). Chaves gerenciadas pelo cliente se comportam de maneira diferente para discos gerenciados do que para recursos de armazenamento do Azure Para obter mais informações, consulte [criptografia do lado do servidor de Azure Managed disks](../../virtual-machines/windows/disk-encryption.md) para Windows ou [criptografia no lado do servidor de Azure Managed disks](../../virtual-machines/linux/disk-encryption.md) para Linux.

## <a name="customer-provided-keys-preview"></a>Chaves fornecidas pelo cliente (versão prévia)

Os clientes que fazem solicitações no armazenamento de BLOBs do Azure têm a opção de fornecer uma chave de criptografia em uma solicitação individual. Incluir a chave de criptografia na solicitação fornece controle granular das configurações de criptografia para operações de armazenamento de BLOBs. As chaves fornecidas pelo cliente (versão prévia) podem ser armazenadas em Azure Key Vault ou em outro repositório de chaves.

Para obter um exemplo que mostra como especificar uma chave fornecida pelo cliente em uma solicitação para o armazenamento de BLOBs, consulte [especificar uma chave fornecida pelo cliente em uma solicitação para o armazenamento de BLOBs com o .net](../blobs/storage-blob-customer-provided-key.md). 

### <a name="encrypting-read-and-write-operations"></a>Criptografando operações de leitura e gravação

Quando um aplicativo cliente fornece uma chave de criptografia na solicitação, o armazenamento do Azure executa criptografia e descriptografia de forma transparente ao ler e gravar dados de BLOB. O armazenamento do Azure grava um hash SHA-256 da chave de criptografia ao lado do conteúdo do blob. O hash é usado para verificar se todas as operações subsequentes no blob usam a mesma chave de criptografia. 

O armazenamento do Azure não armazena ou gerencia a chave de criptografia que o cliente envia com a solicitação. A chave é descartada com segurança assim que o processo de criptografia ou descriptografia estiver concluído.

Quando um cliente cria ou atualiza um BLOB usando uma chave fornecida pelo cliente, as solicitações de leitura e gravação subsequentes para esse blob também devem fornecer a chave. Se a chave não for fornecida em uma solicitação para um blob que já foi criptografado com uma chave fornecida pelo cliente, a solicitação falhará com o código de erro 409 (conflito).

Se o aplicativo cliente enviar uma chave de criptografia na solicitação e a conta de armazenamento também for criptografada usando uma chave gerenciada pela Microsoft ou uma chave gerenciada pelo cliente, o armazenamento do Azure usará a chave fornecida na solicitação de criptografia e descriptografia.

Para enviar a chave de criptografia como parte da solicitação, um cliente deve estabelecer uma conexão segura com o armazenamento do Azure usando HTTPS.

Cada instantâneo de blob pode ter sua própria chave de criptografia.

### <a name="request-headers-for-specifying-customer-provided-keys"></a>Cabeçalhos de solicitação para especificar chaves fornecidas pelo cliente

Para chamadas REST, os clientes podem usar os seguintes cabeçalhos para transmitir informações de chave de criptografia com segurança em uma solicitação para o armazenamento de BLOBs:

|Cabeçalho do Pedido | Descrição |
|---------------|-------------|
|`x-ms-encryption-key` |Necessário para solicitações de gravação e leitura. Um valor de chave de criptografia AES-256 codificado na base64. |
|`x-ms-encryption-key-sha256`| Necessário para solicitações de gravação e leitura. A SHA256 codificada em base64 da chave de criptografia. |
|`x-ms-encryption-algorithm` | Necessário para solicitações de gravação, opcional para solicitações de leitura. Especifica o algoritmo a ser usado ao criptografar dados usando a chave especificada. Deve ser AES256. |

A especificação de chaves de criptografia na solicitação é opcional. No entanto, se você especificar um dos cabeçalhos listados acima para uma operação de gravação, deverá especificar todos eles.

### <a name="blob-storage-operations-supporting-customer-provided-keys"></a>Operações de armazenamento de BLOBs que dão suporte a chaves fornecidas pelo cliente

As operações de armazenamento de blob a seguir dão suporte ao envio de chaves de criptografia fornecidas pelo cliente em uma solicitação:

- [Colocar o Blob](/rest/api/storageservices/put-blob)
- [Colocar lista de blocos](/rest/api/storageservices/put-block-list)
- [Colocar bloco](/rest/api/storageservices/put-block)
- [Colocar bloco da URL](/rest/api/storageservices/put-block-from-url)
- [Colocar página](/rest/api/storageservices/put-page)
- [Colocar página da URL](/rest/api/storageservices/put-page-from-url)
- [Bloco de acréscimo](/rest/api/storageservices/append-block)
- [Definir propriedades de BLOB](/rest/api/storageservices/set-blob-properties)
- [Definir metadados de BLOB](/rest/api/storageservices/set-blob-metadata)
- [Obter blob](/rest/api/storageservices/get-blob)
- [Get Blob Properties](/rest/api/storageservices/get-blob-properties) (Obter Propriedades do Blob)
- [Obter metadados de BLOB](/rest/api/storageservices/get-blob-metadata)
- [Blob de Instantâneo](/rest/api/storageservices/snapshot-blob)

### <a name="rotate-customer-provided-keys"></a>Girar chaves fornecidas pelo cliente

Para girar uma chave de criptografia passada na solicitação, baixe o blob e carregue-o novamente com a nova chave de criptografia.

> [!IMPORTANT]
> O portal do Azure não pode ser usado para ler ou gravar em um contêiner ou BLOB que é criptografado com uma chave fornecida na solicitação.
>
> Certifique-se de proteger a chave de criptografia que você fornece em uma solicitação de armazenamento de BLOBs em um repositório de chaves seguro, como Azure Key Vault. Se você tentar uma operação de gravação em um contêiner ou BLOB sem a chave de criptografia, a operação falhará e você perderá o acesso ao objeto.

## <a name="azure-storage-encryption-versus-disk-encryption"></a>Criptografia de armazenamento do Azure versus criptografia de disco

A criptografia de armazenamento do Azure criptografa os blobs de páginas que retornam discos de máquina virtual do Azure. Além disso, todos os discos de máquina virtual do Azure, incluindo discos temporários locais, podem, opcionalmente, ser criptografados com [Azure Disk Encryption](../../security/azure-security-disk-encryption-overview.md). Azure Disk Encryption usa o [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) padrão do setor no Windows e [DM-cript](https://en.wikipedia.org/wiki/Dm-crypt) no Linux para fornecer soluções de criptografia baseadas no sistema operacional integradas ao Azure Key Vault.

## <a name="next-steps"></a>Passos seguintes

- [O que é o Azure Key Vault?](../../key-vault/key-vault-overview.md)
- [Configurar chaves geridas pelo cliente para a encriptação do Armazenamento do Microsoft Azure no portal do Azure](storage-encryption-keys-portal.md)
- [Configurar chaves geridas pelo cliente para a encriptação do Armazenamento do Microsoft Azure no PowerShell](storage-encryption-keys-powershell.md)
- [Configurar chaves geridas pelo cliente para a encriptação do Armazenamento do Microsoft Azure na CLI do Azure](storage-encryption-keys-cli.md)
