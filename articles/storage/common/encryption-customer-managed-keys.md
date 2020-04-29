---
title: Utilize chaves geridas pelo cliente com o Cofre de Chaves Azure para gerir a encriptação da conta
titleSuffix: Azure Storage
description: Pode utilizar a sua própria chave de encriptação para proteger os dados na sua conta de armazenamento. Quando especifica uma chave gerida pelo cliente, essa chave é utilizada para proteger e controlar o acesso à chave que encripta os seus dados. As chaves geridas pelo cliente oferecem uma maior flexibilidade para gerir os controlos de acesso.
services: storage
author: tamram
ms.service: storage
ms.date: 03/12/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: b2755d5aa5dbaa669fa2fdd8b84596e040b5dd6b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81456826"
---
# <a name="use-customer-managed-keys-with-azure-key-vault-to-manage-azure-storage-encryption"></a>Utilize chaves geridas pelo cliente com cofre de chaves Azure para gerir encriptação de armazenamento azure

Pode utilizar a sua própria chave de encriptação para proteger os dados na sua conta de armazenamento. Quando especifica uma chave gerida pelo cliente, essa chave é utilizada para proteger e controlar o acesso à chave que encripta os seus dados. As chaves geridas pelo cliente oferecem uma maior flexibilidade para gerir os controlos de acesso.

Você deve usar o Cofre chave Azure para armazenar as suas chaves geridas pelo cliente. Pode criar as suas próprias chaves e armazená-las num cofre de chaves, ou pode usar as APIs do Cofre de Chaves Azure para gerar chaves. A conta de armazenamento e o cofre chave devem estar na mesma região e no mesmo inquilino azure Ative Directory (Azure AD), mas podem estar em diferentes subscrições. Para mais informações sobre o Cofre de Chaves Azure, veja [o que é o Cofre de Chaves Azure?](../../key-vault/general/overview.md)

## <a name="about-customer-managed-keys"></a>Sobre chaves geridas pelo cliente

O diagrama seguinte mostra como o Azure Storage utiliza o Azure Ative Directory e o Azure Key Vault para fazer pedidos utilizando a chave gerida pelo cliente:

![Diagrama mostrando como as chaves geridas pelo cliente funcionam no Armazenamento Azure](media/encryption-customer-managed-keys/encryption-customer-managed-keys-diagram.png)

A lista que se segue explica os passos numerados no diagrama:

1. Um administrador da Azure Key Vault concede permissões para encriptar chaves da identidade gerida que está associada à conta de armazenamento.
2. Um administrador de armazenamento Azure configura a encriptação com uma chave gerida pelo cliente para a conta de armazenamento.
3. O Azure Storage utiliza a identidade gerida que está associada à conta de armazenamento para autenticar o acesso ao Cofre de Chaves Azure via Diretório Ativo Azure.
4. O Azure Storage envolve a chave de encriptação da conta com a chave do cliente no Cofre de Chaves Azure.
5. Para operações de leitura/escrita, o Azure Storage envia pedidos ao Azure Key Vault para desembrulhar a chave de encriptação da conta para realizar operações de encriptação e desencriptação.

## <a name="create-an-account-that-supports-customer-managed-keys-for-queues-and-tables"></a>Criar uma conta que suporte chaves geridas pelo cliente para filas e mesas

Os dados armazenados nos serviços de Fila e Mesa não são automaticamente protegidos por uma chave gerida pelo cliente quando as chaves geridas pelo cliente estão ativadas para a conta de armazenamento. Pode configurar opcionalmente estes serviços no momento em que cria a conta de armazenamento para ser incluída nesta proteção.

Para obter mais informações sobre como criar uma conta de armazenamento que suporte chaves geridas pelo cliente para filas e mesas, consulte [Criar uma conta que suporte as chaves geridas pelo cliente para mesas e filas.](account-encryption-key-create.md)

Os dados dos serviços Blob e File são sempre protegidos por chaves geridas pelo cliente quando as chaves geridas pelo cliente são configuradas para a conta de armazenamento.

## <a name="enable-customer-managed-keys-for-a-storage-account"></a>Ativar as chaves geridas pelo cliente para uma conta de armazenamento

As chaves geridas pelo cliente só podem ser ativadas nas contas de armazenamento existentes. O cofre-chave deve ser aprovisionado com políticas de acesso que concedam permissões-chave para a identidade gerida que está associada à conta de armazenamento. A identidade gerida só está disponível após a criação da conta de armazenamento.

Quando configura uma chave gerida pelo cliente, o Azure Storage envolve a chave de encriptação de dados de raiz para a conta com a chave gerida pelo cliente no cofre de chaves associado. Ativar as chaves geridas pelo cliente não afeta o desempenho e entra em vigor imediatamente.

Quando modifica a chave que está a ser utilizada para encriptação de Armazenamento Azure, permitindo ou desativando as chaves geridas pelo cliente, atualizando a versão chave ou especificando uma tecla diferente, então a encriptação da chave raiz muda, mas os dados na sua conta De armazenamento Azure não precisam de ser novamente encriptados.

Quando ativa ou desativa as chaves geridas pelo cliente, ou quando modifica a chave ou a versão chave, a proteção da chave de encriptação da raiz muda, mas os dados na sua conta De armazenamento do Azure não precisam de ser novamente encriptados.

Para aprender a usar chaves geridas pelo cliente com o Cofre chave Azure para encriptação de armazenamento azure, consulte um destes artigos:

- [Configure chaves geridas pelo cliente com cofre chave para encriptação de armazenamento Azure do portal Azure](storage-encryption-keys-portal.md)
- [Configure chaves geridas pelo cliente com key vault para encriptação de armazenamento Azure da PowerShell](storage-encryption-keys-powershell.md)
- [Configure chaves geridas pelo cliente com cofre chave para encriptação de armazenamento Azure do Azure CLI](storage-encryption-keys-cli.md)

> [!IMPORTANT]
> As chaves geridas pelo cliente dependem de identidades geridas para os recursos Azure, uma característica da Azure AD. As identidades geridas não suportam atualmente cenários transversais. Quando configura as chaves geridas pelo cliente no portal Azure, uma identidade gerida é automaticamente atribuída à sua conta de armazenamento sob as capas. Se posteriormente deslocar a conta de subscrição, de recursos ou de armazenamento de um diretório Azure AD para outro, a identidade gerida associada à conta de armazenamento não é transferida para o novo inquilino, pelo que as chaves geridas pelo cliente podem deixar de funcionar. Para obter mais informações, consulte **a Transferência de uma subscrição entre diretórios da AD Azure** em [FAQs e questões conhecidas com identidades geridas para recursos Azure](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).  

## <a name="store-customer-managed-keys-in-azure-key-vault"></a>Armazenar chaves geridas pelo cliente no Cofre de Chaves Azure

Para ativar as chaves geridas pelo cliente numa conta de armazenamento, deve utilizar um Cofre de Chave Azure para armazenar as suas chaves. Deve ativar as propriedades **Soft Delete** e **Não purgar** as propriedades no cofre da chave.

Apenas as teclas RSA e RSA-HSM de 2048 são suportadas com encriptação de Armazenamento Azure. Para mais informações sobre as chaves, consulte **as chaves key vault** em [chaves, segredos e certificados do Cofre chave Azure.](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)

## <a name="rotate-customer-managed-keys"></a>Rode as chaves geridas pelo cliente

Pode rodar uma chave gerida pelo cliente no Cofre de Chaves Azure de acordo com as suas políticas de conformidade. Quando a chave estiver rodada, tem de atualizar a conta de armazenamento para utilizar a nova versão-chave URI. Para aprender a atualizar a conta de armazenamento para utilizar uma nova versão da chave no portal Azure, consulte a secção intitulada **Atualizar a versão chave** em Chaves [geridas pelo cliente configure para o Armazenamento Azure utilizando o portal Azure](storage-encryption-keys-portal.md).

Rodar a tecla não desencadeia a reencriptação de dados na conta de armazenamento. Não são necessárias mais medidas por parte do utilizador.

## <a name="revoke-access-to-customer-managed-keys"></a>Revogar o acesso às chaves geridas pelo cliente

Pode revogar o acesso da conta de armazenamento à chave gerida pelo cliente a qualquer momento. Após a revogação do acesso às chaves geridas pelo cliente, ou depois de a chave ter sido desativada ou eliminada, os clientes não podem ligar para operações que leiam ou escrevam a uma bolha ou aos seus metadados. As tentativas de chamada de qualquer uma das seguintes operações falharão com o código de erro 403 (Proibido) para todos os utilizadores:

- [Lista Blobs,](/rest/api/storageservices/list-blobs)quando `include=metadata` chamado com o parâmetro no pedido URI
- [Obter Blob](/rest/api/storageservices/get-blob)
- [Get Blob Properties](/rest/api/storageservices/get-blob-properties) (Obter Propriedades do Blob)
- [Obter Metadados Blob](/rest/api/storageservices/get-blob-metadata)
- [Definir Metadados Blob](/rest/api/storageservices/set-blob-metadata)
- [Snapshot Blob,](/rest/api/storageservices/snapshot-blob)quando `x-ms-meta-name` chamado com o cabeçalho de pedido
- [Copy Blob](/rest/api/storageservices/copy-blob)
- [Copiar Blob de URL](/rest/api/storageservices/copy-blob-from-url)
- [Definir Camada de Blob](/rest/api/storageservices/set-blob-tier)
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

## <a name="customer-managed-keys-for-azure-managed-disks"></a>Chaves geridas pelo cliente para discos geridos pelo Azure

As chaves geridas pelo cliente também estão disponíveis para gerir a encriptação dos discos geridos pelo Azure. As chaves geridas pelo cliente comportam-se de forma diferente para discos geridos do que para os recursos de Armazenamento Azure. Para obter mais informações, consulte a [encriptação do lado do Servidor do Azure gerido por discos](../../virtual-machines/windows/disk-encryption.md) para encriptação lateral do Windows ou [Server de discos geridos](../../virtual-machines/linux/disk-encryption.md) pelo Azure para o Linux.

## <a name="next-steps"></a>Passos seguintes

- [Configure chaves geridas pelo cliente com cofre chave para encriptação de armazenamento Azure do portal Azure](storage-encryption-keys-portal.md)
- [Configure chaves geridas pelo cliente com key vault para encriptação de armazenamento Azure da PowerShell](storage-encryption-keys-powershell.md)
- [Configure chaves geridas pelo cliente com cofre chave para encriptação de armazenamento Azure do Azure CLI](storage-encryption-keys-cli.md)
- [Encriptação azure storage para dados em repouso](storage-service-encryption.md)
