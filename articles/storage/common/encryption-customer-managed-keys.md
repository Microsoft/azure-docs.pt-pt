---
title: Use chaves geridas pelo cliente com Azure Key Vault para gerir encriptação de conta
titleSuffix: Azure Storage
description: Pode utilizar a sua própria chave de encriptação para proteger os dados na sua conta de armazenamento. Quando especifica uma chave gerida pelo cliente, essa chave é usada para proteger e controlar o acesso à chave que encripta os seus dados. As chaves geridas pelo cliente oferecem uma maior flexibilidade para gerir os controlos de acesso.
services: storage
author: tamram
ms.service: storage
ms.date: 07/20/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: af70b1746b2ac847d964975aaf1b2186aa89be01
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87292740"
---
# <a name="use-customer-managed-keys-with-azure-key-vault-to-manage-azure-storage-encryption"></a>Utilize chaves geridas pelo cliente com cofre de chaves Azure para gerir a encriptação de armazenamento Azure

Pode utilizar a sua própria chave de encriptação para proteger os dados na sua conta de armazenamento. Quando especifica uma chave gerida pelo cliente, essa chave é usada para proteger e controlar o acesso à chave que encripta os seus dados. As chaves geridas pelo cliente oferecem uma maior flexibilidade para gerir os controlos de acesso.

Tem de utilizar o Cofre da Chave Azure para armazenar as suas chaves geridas pelo cliente. Pode criar as suas próprias chaves e armazená-las num cofre de chaves, ou pode usar as APIs do Cofre de Chaves Azure para gerar chaves. A conta de armazenamento e o cofre-chave devem estar na mesma região e no mesmo inquilino do Azure Ative Directory (Azure AD), mas podem estar em diferentes subscrições. Para mais informações sobre o Azure Key Vault, veja [o que é Azure Key Vault?](../../key-vault/general/overview.md)

## <a name="about-customer-managed-keys"></a>Sobre chaves geridas pelo cliente

O seguinte diagrama mostra como o Azure Storage utiliza o Azure Ative Directory e o Azure Key Vault para fazer pedidos utilizando a chave gerida pelo cliente:

![Diagrama mostrando como as chaves geridas pelo cliente funcionam no Azure Storage](media/encryption-customer-managed-keys/encryption-customer-managed-keys-diagram.png)

A lista seguinte explica os passos numerados no diagrama:

1. Um administrador do Azure Key Vault concede permissões a chaves de encriptação da identidade gerida associada à conta de armazenamento.
2. Um admin de armazenamento Azure configura a encriptação com uma chave gerida pelo cliente para a conta de armazenamento.
3. O Azure Storage utiliza a identidade gerida associada à conta de armazenamento para autenticar o acesso ao Cofre da Chave Azure via Azure Ative Directory.
4. O Azure Storage embrulha a chave de encriptação da conta com a chave do cliente no Cofre da Chave Azure.
5. Para operações de leitura/escrita, o Azure Storage envia pedidos ao Azure Key Vault para desembrulhar a chave de encriptação da conta para realizar operações de encriptação e desencriptação.

## <a name="create-an-account-that-supports-customer-managed-keys-for-queues-and-tables"></a>Crie uma conta que suporte chaves geridas pelo cliente para filas e tabelas

Os dados armazenados nos serviços de fila e mesa não são automaticamente protegidos por uma chave gerida pelo cliente quando as chaves geridas pelo cliente estão ativadas para a conta de armazenamento. Pode configurar opcionalmente estes serviços no momento em que criar a conta de armazenamento a incluir nesta proteção.

Para obter mais informações sobre como criar uma conta de armazenamento que suporte chaves geridas pelo cliente para filas e tabelas, consulte [Criar uma conta que suporte chaves geridas pelo cliente para tabelas e filas.](account-encryption-key-create.md)

Os dados nos serviços Blob e File estão sempre protegidos por chaves geridas pelo cliente quando as chaves geridas pelo cliente são configuradas para a conta de armazenamento.

## <a name="enable-customer-managed-keys-for-a-storage-account"></a>Ativar chaves geridas pelo cliente para uma conta de armazenamento

Ao configurar uma chave gerida pelo cliente, o Azure Storage embrulha a chave de encriptação de dados raiz para a conta com a chave gerida pelo cliente no cofre de chaves associado. Ativar as chaves geridas pelo cliente não afeta o desempenho e entra em vigor imediatamente.

Quando ativa ou desativa as teclas geridas pelo cliente, ou quando modifica a chave ou a versão chave, a proteção da chave de encriptação raiz muda, mas os dados na sua conta de Armazenamento Azure não precisam de ser reencrim encriptados.

As chaves geridas pelo cliente só podem ser ativadas nas contas de armazenamento existentes. O cofre-chave deve ser configurado com políticas de acesso que concedam permissões à identidade gerida associada à conta de armazenamento. A identidade gerida só está disponível após a criação da conta de armazenamento.

Pode alternar entre as teclas geridas pelo cliente e as teclas geridas pela Microsoft a qualquer momento. Para obter mais informações sobre as teclas geridas pela Microsoft, consulte [sobre a gestão de chaves de encriptação](storage-service-encryption.md#about-encryption-key-management).

Para aprender a usar chaves geridas pelo cliente com cofre de chaves Azure para encriptação de armazenamento Azure, consulte um destes artigos:

- [Configure as chaves geridas pelo cliente com o Key Vault para encriptação de armazenamento Azure a partir do portal Azure](storage-encryption-keys-portal.md)
- [Configure as chaves geridas pelo cliente com o Key Vault para encriptação de armazenamento Azure da PowerShell](storage-encryption-keys-powershell.md)
- [Configure as chaves geridas pelo cliente com o Key Vault para encriptação de armazenamento Azure da Azure CLI](storage-encryption-keys-cli.md)

> [!IMPORTANT]
> As chaves geridas pelo cliente dependem de identidades geridas para os recursos da Azure, uma característica da Azure AD. As identidades geridas não suportam atualmente cenários de diretórios cruzados. Quando configura as chaves geridas pelo cliente no portal Azure, uma identidade gerida é automaticamente atribuída à sua conta de armazenamento sob as capas. Se posteriormente mover a subscrição, o grupo de recursos ou a conta de armazenamento de um diretório AD Azure para outro, a identidade gerida associada à conta de armazenamento não é transferida para o novo inquilino, pelo que as chaves geridas pelo cliente podem deixar de funcionar. Para obter mais informações, consulte **a transferência de uma subscrição entre diretórios AD Azure** em [FAQs e questões conhecidas com identidades geridas para recursos Azure](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).  

## <a name="store-customer-managed-keys-in-azure-key-vault"></a>Armazenar chaves geridas pelo cliente no Cofre da Chave Azure

Para ativar as chaves geridas pelo cliente numa conta de armazenamento, tem de utilizar um cofre de chaves Azure para guardar as suas chaves. Tem de ativar as propriedades **Soft Delete** e **Não Purgar** no cofre da chave.

A encriptação de armazenamento Azure suporta chaves RSA e RSA-HSM dos tamanhos 2048, 3072 e 4096. Para obter mais informações sobre as chaves, consulte **as chaves do Cofre chave** em chaves [Azure Key Vault, segredos e certificados](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).

A utilização do Cofre da Chave Azure tem custos associados. Para obter mais informações, consulte [os preços do Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="rotate-customer-managed-keys"></a>Rode as teclas geridas pelo cliente

Pode rodar uma chave gerida pelo cliente no Cofre de Chaves Azure de acordo com as suas políticas de conformidade. Tem duas opções para rodar uma chave gerida pelo cliente:

- **Rotação automática:** Para configurar a rotação automática das teclas geridas pelo cliente, omita a versão chave quando ativar a encriptação com as chaves geridas pelo cliente para a conta de armazenamento. Se a versão da chave for omitida, o Armazenamento do Microsoft Azure verificará diariamente o Azure Key Vault para confirmar se existe uma nova versão de uma chave gerida pelo cliente. Se estiver disponível uma nova versão da chave, o Armazenamento do Microsoft Azure utilizará automaticamente a versão mais recente da chave.
- **Rotação manual:** Para utilizar uma versão chave específica para encriptação do Azure Storage, especifique essa versão chave quando ativar a encriptação com as chaves geridas pelo cliente para a conta de armazenamento. Se especificar a versão chave, o Azure Storage utiliza essa versão para encriptação até atualizar manualmente a versão chave.

    Quando a chave estiver rodada manualmente, tem de atualizar a conta de armazenamento para utilizar a nova versão URI da chave. Para saber como atualizar a conta de armazenamento para utilizar uma nova versão da chave no portal Azure, consulte [atualização manual da versão chave](storage-encryption-keys-portal.md#manually-update-the-key-version).

A rotação de uma chave gerida pelo cliente não desencadeia a reencriminação de dados na conta de armazenamento. Não é necessária nenhuma outra ação por parte do utilizador.

## <a name="revoke-access-to-customer-managed-keys"></a>Revogar o acesso às chaves geridas pelo cliente

Pode revogar o acesso da conta de armazenamento à chave gerida pelo cliente a qualquer momento. Após o acesso às chaves geridas pelo cliente ser revogado, ou depois de a chave ter sido desativada ou eliminada, os clientes não podem ligar para operações que leiam ou escrevam para uma bolha ou para os seus metadados. As tentativas de chamar qualquer uma das seguintes operações falharão com o código de erro 403 (Proibido) para todos os utilizadores:

- [List Blobs](/rest/api/storageservices/list-blobs), quando chamado com o `include=metadata` parâmetro no pedido URI
- [Obter Blob](/rest/api/storageservices/get-blob)
- [Get Blob Properties](/rest/api/storageservices/get-blob-properties) (Obter Propriedades do Blob)
- [Obtenha metadados blob](/rest/api/storageservices/get-blob-metadata)
- [Definir metadados blob](/rest/api/storageservices/set-blob-metadata)
- [Snapshot Blob](/rest/api/storageservices/snapshot-blob), quando chamado com o cabeçalho do `x-ms-meta-name` pedido
- [Bolha de cópia](/rest/api/storageservices/copy-blob)
- [Bolha de cópia da URL](/rest/api/storageservices/copy-blob-from-url)
- [Set Blob Tier](/rest/api/storageservices/set-blob-tier) (Definir Camada de Blob)
- [Colocar Bloco](/rest/api/storageservices/put-block)
- [Coloque o bloco de URL](/rest/api/storageservices/put-block-from-url)
- [Bloco de Apêndice](/rest/api/storageservices/append-block)
- [Bloco de apêndice de URL](/rest/api/storageservices/append-block-from-url)
- [Coloque Blob](/rest/api/storageservices/put-blob)
- [Colocar página](/rest/api/storageservices/put-page)
- [Colocar página a partir de URL](/rest/api/storageservices/put-page-from-url)
- [Bolha de cópia incremental](/rest/api/storageservices/incremental-copy-blob)

Para voltar a ligar para estas operações, restabeleça o acesso à chave gerida pelo cliente.

Todas as operações de dados que não estejam listadas nesta secção podem prosseguir após a revogação das chaves geridas pelo cliente ou desativação ou desativação de uma chave.

Para revogar o acesso às chaves geridas pelo cliente, utilize [o PowerShell](storage-encryption-keys-powershell.md#revoke-customer-managed-keys) ou [o Azure CLI](storage-encryption-keys-cli.md#revoke-customer-managed-keys).

## <a name="customer-managed-keys-for-azure-managed-disks"></a>Chaves geridas pelo cliente para discos geridos pela Azure

As chaves geridas pelo cliente também estão disponíveis para gerir a encriptação dos discos geridos pela Azure. As chaves geridas pelo cliente comportam-se de forma diferente para discos geridos do que para os recursos de Armazenamento Azure. Para obter mais informações, consulte [a encriptação do lado do Servidor dos discos geridos pelo Azure](../../virtual-machines/windows/disk-encryption.md) para encriptação lateral do Windows ou servidor dos discos geridos pelo [Azure](../../virtual-machines/linux/disk-encryption.md) para o Linux.

## <a name="next-steps"></a>Passos seguintes

- [Configure as chaves geridas pelo cliente com o Key Vault para encriptação de armazenamento Azure a partir do portal Azure](storage-encryption-keys-portal.md)
- [Configure as chaves geridas pelo cliente com o Key Vault para encriptação de armazenamento Azure da PowerShell](storage-encryption-keys-powershell.md)
- [Configure as chaves geridas pelo cliente com o Key Vault para encriptação de armazenamento Azure da Azure CLI](storage-encryption-keys-cli.md)
- [Azure Storage encryption for data at rest](storage-service-encryption.md) (Encriptação do Armazenamento do Azure para dados inativos)
