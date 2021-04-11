---
title: Chaves geridas pelo cliente para encriptação de conta
titleSuffix: Azure Storage
description: Pode utilizar a sua própria chave de encriptação para proteger os dados na sua conta de armazenamento. Quando especifica uma chave gerida pelo cliente, essa chave é utilizada para proteger e controlar o acesso à chave que encripta os seus dados. As chaves geridas pelo cliente oferecem uma maior flexibilidade para gerir os controlos de acesso.
services: storage
author: tamram
ms.service: storage
ms.date: 03/30/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 07f8faf503bdea6be8263afa6240594956b61391
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106059450"
---
# <a name="customer-managed-keys-for-azure-storage-encryption"></a>Chaves geridas pelo cliente para encriptação de armazenamento Azure

Pode utilizar a sua própria chave de encriptação para proteger os dados na sua conta de armazenamento. Quando especifica uma chave gerida pelo cliente, essa chave é utilizada para proteger e controlar o acesso à chave que encripta os seus dados. As chaves geridas pelo cliente oferecem uma maior flexibilidade para gerir os controlos de acesso.

Deve utilizar uma das seguintes lojas de chaves Azure para armazenar as suas chaves geridas pelo cliente:

- [Azure Key Vault](../../key-vault/general/overview.md)
- [Módulo de segurança gerido por hardware gerido por Azure Key (HSM) (pré-visualização)](../../key-vault/managed-hsm/overview.md)

Pode criar as suas próprias chaves e armazená-las no cofre de chaves ou na gestão do HSM, ou pode utilizar as APIs do Cofre de Chaves Azure para gerar chaves. A conta de armazenamento e o cofre-chave ou o HSM gerido devem estar na mesma região e no mesmo inquilino do Azure Ative Directory (Azure AD), mas podem estar em diferentes subscrições.

> [!IMPORTANT]
>
> A encriptação com teclas geridas pelo cliente armazenadas no Azure Key Vault Managed HSM está atualmente em **PREVIEW**. Consulte os [Termos Complementares de Utilização para o Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para termos legais aplicáveis às funcionalidades do Azure que estejam em versão beta, pré-visualização ou ainda não lançadas em disponibilidade geral.
>
> Azure Key Vault e Azure Key Vault Gerido HSM suportam as mesmas APIs e interfaces de gestão para configuração.

## <a name="about-customer-managed-keys"></a>Sobre chaves geridas pelo cliente

O seguinte diagrama mostra como o Azure Storage utiliza o Azure Ative Directory e um cofre chave ou gerido o HSM para fazer pedidos utilizando a chave gerida pelo cliente:

![Diagrama mostrando como as chaves geridas pelo cliente funcionam no Azure Storage](media/customer-managed-keys-overview/encryption-customer-managed-keys-diagram.png)

A lista seguinte explica os passos numerados no diagrama:

1. Um administrador do Azure Key Vault concede permissões a chaves de encriptação da identidade gerida associada à conta de armazenamento.
2. Um admin de armazenamento Azure configura a encriptação com uma chave gerida pelo cliente para a conta de armazenamento.
3. O Azure Storage utiliza a identidade gerida associada à conta de armazenamento para autenticar o acesso ao Cofre da Chave Azure via Azure Ative Directory.
4. O Azure Storage embrulha a chave de encriptação da conta com a chave do cliente no Cofre da Chave Azure.
5. Para operações de leitura/escrita, o Azure Storage envia pedidos ao Azure Key Vault para desembrulhar a chave de encriptação da conta para realizar operações de encriptação e desencriptação.

## <a name="customer-managed-keys-for-queues-and-tables"></a>Chaves geridas pelo cliente para filas e mesas

Os dados armazenados no armazenamento de fila e mesa não são automaticamente protegidos por uma chave gerida pelo cliente quando as chaves geridas pelo cliente estão ativadas para a conta de armazenamento. Pode configurar opcionalmente estes serviços para serem incluídos nesta proteção no momento em que criar a conta de armazenamento.

Para obter mais informações sobre como criar uma conta de armazenamento que suporte chaves geridas pelo cliente para filas e tabelas, consulte [Criar uma conta que suporte chaves geridas pelo cliente para tabelas e filas.](account-encryption-key-create.md)

Os dados relativos ao armazenamento blob e aos ficheiros Azure estão sempre protegidos por chaves geridas pelo cliente quando as chaves geridas pelo cliente são configuradas para a conta de armazenamento.

## <a name="enable-customer-managed-keys-for-a-storage-account"></a>Ativar chaves geridas pelo cliente para uma conta de armazenamento

Ao configurar uma chave gerida pelo cliente, o Azure Storage embrulha a chave de encriptação de dados raiz para a conta com a chave gerida pelo cliente no cofre de chaves associado ou gerido pelo HSM. Ativar as chaves geridas pelo cliente não afeta o desempenho e entra em vigor imediatamente.

Quando ativa ou desativa as teclas geridas pelo cliente, ou quando modifica a chave ou a versão chave, a proteção da chave de encriptação raiz muda, mas os dados na sua conta de Armazenamento Azure não precisam de ser reencrim encriptados.

As chaves geridas pelo cliente só podem ser ativadas nas contas de armazenamento existentes. O cofre-chave ou o HSM gerido devem ser configurados para conceder permissões à identidade gerida associada à conta de armazenamento. A identidade gerida só está disponível após a criação da conta de armazenamento.

Pode alternar entre as teclas geridas pelo cliente e as teclas geridas pela Microsoft a qualquer momento. Para obter mais informações sobre as teclas geridas pela Microsoft, consulte [sobre a gestão de chaves de encriptação](storage-service-encryption.md#about-encryption-key-management).

Para aprender a configurar a encriptação do Armazenamento Azure com chaves geridas pelo cliente num cofre de chaves, consulte [a encriptação Configure com as teclas geridas pelo cliente armazenadas no Cofre da Chave Azure](customer-managed-keys-configure-key-vault.md). Para configurar as chaves geridas pelo cliente num HSM gerido, consulte [encriptação configure com teclas geridas pelo cliente armazenadas em Azure Key Vault Managed HSM (pré-visualização)](customer-managed-keys-configure-key-vault-hsm.md).

> [!IMPORTANT]
> As chaves geridas pelo cliente dependem de identidades geridas para os recursos da Azure, uma característica da Azure AD. As identidades geridas não suportam atualmente cenários de diretórios cruzados. Quando configura as chaves geridas pelo cliente no portal Azure, uma identidade gerida é automaticamente atribuída à sua conta de armazenamento sob as capas. Se posteriormente mover a subscrição, o grupo de recursos ou a conta de armazenamento de um diretório AD Azure para outro, a identidade gerida associada à conta de armazenamento não é transferida para o novo inquilino, pelo que as chaves geridas pelo cliente podem deixar de funcionar. Para obter mais informações, consulte **a transferência de uma subscrição entre diretórios AD Azure** em [FAQs e questões conhecidas com identidades geridas para recursos Azure](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).  

A encriptação de armazenamento Azure suporta chaves RSA e RSA-HSM dos tamanhos 2048, 3072 e 4096. Para obter mais informações sobre as teclas, consulte [sobre as teclas.](../../key-vault/keys/about-keys.md)

A utilização de um cofre-chave ou de um HSM gerido tem custos associados. Para obter mais informações, consulte [os preços do Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="update-the-key-version"></a>Atualizar a versão chave

Ao configurar a encriptação com as teclas geridas pelo cliente, tem duas opções para atualizar a versão chave:

- **Atualize automaticamente a versão chave:** Para atualizar automaticamente uma chave gerida pelo cliente quando uma nova versão estiver disponível, omita a versão chave quando ativa a encriptação com as chaves geridas pelo cliente para a conta de armazenamento. Se a versão chave for omitida, então o Azure Storage verifica o cofre de chaves ou gere diariamente o HSM para uma nova versão de uma chave gerida pelo cliente. O Azure Storage utiliza automaticamente a versão mais recente da chave.
- **Atualize manualmente a versão chave:** Para utilizar uma versão específica de uma chave para encriptação do Azure Storage, especifique essa versão chave quando ativar a encriptação com as chaves geridas pelo cliente para a conta de armazenamento. Se especificar a versão chave, o Azure Storage utiliza essa versão para encriptação até atualizar manualmente a versão chave.

    Quando a versão chave for explicitamente especificada, deve atualizar manualmente a conta de armazenamento para utilizar a nova versão chave URI quando uma nova versão for criada. Para saber como atualizar a conta de armazenamento para utilizar uma nova versão da chave, consulte [encriptação configure com teclas geridas pelo cliente armazenadas no Cofre de Chaves Azure](customer-managed-keys-configure-key-vault.md) ou [encriptação Configure com teclas geridas pelo cliente armazenadas no Cofre de Chaves Azure Managed HSM (pré-visualização)](customer-managed-keys-configure-key-vault-hsm.md).

Quando atualiza a versão chave, a proteção da chave de encriptação raiz muda, mas os dados na sua conta de Armazenamento Azure não são reencri encriptados. Não é necessária nenhuma outra ação por parte do utilizador.

> [!NOTE]
> Para rodar uma chave, crie uma nova versão da chave no cofre ou gerido pelo HSM, de acordo com as suas políticas de conformidade. Pode rodar a chave manualmente ou criar uma função para a rodar num horário.

## <a name="revoke-access-to-customer-managed-keys"></a>Revogar o acesso às chaves geridas pelo cliente

Pode revogar o acesso da conta de armazenamento à chave gerida pelo cliente a qualquer momento. Após o acesso às chaves geridas pelo cliente ser revogado, ou depois de a chave ter sido desativada ou eliminada, os clientes não podem ligar para operações que leiam ou escrevam para uma bolha ou para os seus metadados. As tentativas de chamar qualquer uma das seguintes operações falharão com o código de erro 403 (Proibido) para todos os utilizadores:

- [List Blobs](/rest/api/storageservices/list-blobs), quando chamado com o `include=metadata` parâmetro no pedido URI
- [Obter Blob](/rest/api/storageservices/get-blob)
- [Get Blob Properties](/rest/api/storageservices/get-blob-properties) (Obter Propriedades do Blob)
- [Obtenha metadados blob](/rest/api/storageservices/get-blob-metadata)
- [Definir metadados blob](/rest/api/storageservices/set-blob-metadata)
- [Snapshot Blob](/rest/api/storageservices/snapshot-blob), quando chamado com o cabeçalho do `x-ms-meta-name` pedido
- [Copiar Blob](/rest/api/storageservices/copy-blob)
- [Bolha de cópia da URL](/rest/api/storageservices/copy-blob-from-url)
- [Set Blob Tier](/rest/api/storageservices/set-blob-tier) (Definir Camada de Blob)
- [Colocar Bloco](/rest/api/storageservices/put-block)
- [Coloque o bloco de URL](/rest/api/storageservices/put-block-from-url)
- [Bloco de Apêndice](/rest/api/storageservices/append-block)
- [Bloco de apêndice de URL](/rest/api/storageservices/append-block-from-url)
- [Colocar o Blob](/rest/api/storageservices/put-blob)
- [Colocar página](/rest/api/storageservices/put-page)
- [Colocar página a partir de URL](/rest/api/storageservices/put-page-from-url)
- [Bolha de cópia incremental](/rest/api/storageservices/incremental-copy-blob)

Para voltar a ligar para estas operações, restabeleça o acesso à chave gerida pelo cliente.

Todas as operações de dados que não estejam listadas nesta secção podem prosseguir após a revogação das chaves geridas pelo cliente ou desativação ou desativação de uma chave.

Para revogar o acesso às chaves geridas pelo cliente, utilize [o PowerShell](./customer-managed-keys-configure-key-vault.md#revoke-customer-managed-keys) ou [o Azure CLI](./customer-managed-keys-configure-key-vault.md#revoke-customer-managed-keys).

## <a name="customer-managed-keys-for-azure-managed-disks"></a>Chaves geridas pelo cliente para discos geridos pela Azure

As chaves geridas pelo cliente também estão disponíveis para gerir a encriptação dos discos geridos pela Azure. As chaves geridas pelo cliente comportam-se de forma diferente para discos geridos do que para os recursos de Armazenamento Azure. Para obter mais informações, consulte [a encriptação do lado do Servidor dos discos geridos pelo Azure](../../virtual-machines/disk-encryption.md) para encriptação lateral do Windows ou servidor dos discos geridos pelo [Azure](../../virtual-machines/disk-encryption.md) para o Linux.

## <a name="next-steps"></a>Passos seguintes

- [Azure Storage encryption for data at rest](storage-service-encryption.md) (Encriptação do Armazenamento do Azure para dados inativos)
- [Configurar a encriptação com chaves geridas pelo cliente armazenadas no Azure Key Vault](customer-managed-keys-configure-key-vault.md)
- [Configure a encriptação com chaves geridas pelo cliente armazenadas no Azure Key Vault Managed HSM (pré-visualização)](customer-managed-keys-configure-key-vault-hsm.md)
