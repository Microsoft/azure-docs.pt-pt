---
title: Âmbitos de encriptação para armazenamento blob
description: Os âmbitos de encriptação fornecem a capacidade de gerir a encriptação ao nível do recipiente ou de uma bolha individual. Pode utilizar âmbitos de encriptação para criar limites seguros entre dados que residem na mesma conta de armazenamento, mas que pertencem a diferentes clientes.
services: storage
author: tamram
ms.service: storage
ms.date: 03/26/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 16a600d7caf65f880ffb5c2a2abfe5a9774a7795
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105640460"
---
# <a name="encryption-scopes-for-blob-storage"></a>Âmbitos de encriptação para armazenamento blob

Os âmbitos de encriptação permitem-lhe gerir a encriptação com uma chave que é traçada para um recipiente ou uma bolha individual. Pode utilizar âmbitos de encriptação para criar limites seguros entre dados que residem na mesma conta de armazenamento, mas que pertencem a diferentes clientes.

Para obter mais informações sobre o trabalho com âmbitos de encriptação, consulte [Criar e gerir os âmbitos de encriptação.](encryption-scope-manage.md)

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="how-encryption-scopes-work"></a>Como funcionam os âmbitos de encriptação

Por padrão, uma conta de armazenamento é encriptada com uma chave que é telescópio em toda a conta de armazenamento. Quando define um âmbito de encriptação, especifica uma chave que pode ser traçada para um recipiente ou uma bolha individual. Quando o âmbito de encriptação é aplicado a uma bolha, a bolha é encriptada com essa chave. Quando o âmbito de encriptação é aplicado a um recipiente, ele serve como o âmbito padrão para as bolhas nesse recipiente, de modo que todas as bolhas que são carregadas para esse recipiente possam ser encriptadas com a mesma chave. O recipiente pode ser configurado para impor o âmbito de encriptação padrão para todas as bolhas no recipiente, ou para permitir que uma bolha individual seja carregada para o recipiente com um âmbito de encriptação diferente do padrão.

As operações de leitura numa bolha criada com um âmbito de encriptação acontecem de forma transparente, desde que o âmbito de encriptação não seja desativado.

### <a name="key-management"></a>Gestão de chaves

Quando define um âmbito de encriptação, pode especificar se o âmbito está protegido com uma chave gerida pela Microsoft ou com uma chave gerida pelo cliente que é armazenada no Cofre da Chave Azure. Diferentes âmbitos de encriptação na mesma conta de armazenamento podem utilizar chaves geridas pela Microsoft ou geridas pelo cliente. Também pode mudar o tipo de chave usada para proteger um âmbito de encriptação de uma chave gerida pelo cliente para uma chave gerida pela Microsoft, ou vice-versa, a qualquer momento. Para obter mais informações sobre as chaves geridas pelo cliente, consulte [as chaves geridas pelo Cliente para encriptação de armazenamento Azure](../common/customer-managed-keys-overview.md). Para obter mais informações sobre as teclas geridas pela Microsoft, consulte [sobre a gestão de chaves de encriptação](../common/storage-service-encryption.md#about-encryption-key-management).

Se definir um âmbito de encriptação com uma chave gerida pelo cliente, então pode optar por atualizar a versão chave automaticamente ou manualmente. Se optar por atualizar automaticamente a versão chave, o Azure Storage verifica diariamente o cofre de chaves ou gere o HSM para uma nova versão da chave gerida pelo cliente e atualiza automaticamente a chave para a versão mais recente. Para obter mais informações sobre a atualização da versão chave para uma chave gerida pelo cliente, consulte [Atualizar a versão chave](../common/customer-managed-keys-overview.md#update-the-key-version).

Uma conta de armazenamento pode ter até 10.000 âmbitos de encriptação que estão protegidos com chaves geridas pelo cliente para as quais a versão chave é automaticamente atualizada. Se a sua conta de armazenamento já tiver 10.000 âmbitos de encriptação protegidos com chaves geridas pelo cliente que estão a ser atualizadas automaticamente, então a versão chave deve ser atualizada manualmente para quaisquer âmbitos de encriptação adicionais protegidos com chaves geridas pelo cliente.  

### <a name="encryption-scopes-for-containers-and-blobs"></a>Âmbitos de encriptação para contentores e bolhas

Quando criar um recipiente, pode especificar um âmbito de encriptação padrão para as bolhas que são posteriormente enviadas para esse contentor. Quando especificar um âmbito de encriptação padrão para um recipiente, pode decidir como é que o âmbito de encriptação padrão é aplicado:

- Pode exigir que todas as bolhas enviadas para o recipiente utilizem o âmbito de encriptação padrão. Neste caso, todas as bolhas do recipiente são encriptadas com a mesma chave.
- Pode permitir que um cliente substitua o âmbito de encriptação padrão do recipiente, de modo a que uma bolha possa ser carregada com um âmbito de encriptação diferente do âmbito padrão. Neste caso, as bolhas no recipiente podem ser encriptadas com teclas diferentes.

A tabela seguinte resume o comportamento de uma operação de upload de bolhas, dependendo da configuração do âmbito de encriptação padrão para o recipiente:

| O âmbito de encriptação definido no recipiente é... | Carregar uma bolha com o âmbito de encriptação padrão... | Carregar uma bolha com um âmbito de encriptação diferente do âmbito padrão... |
|--|--|--|
| Um âmbito de encriptação padrão com sobreposições permitidas | Tem sucesso | Tem sucesso |
| Um âmbito de encriptação padrão com sobreposições proibidas | Tem sucesso | Falha |

Deve ser especificado um âmbito de encriptação predefinido para um recipiente no momento da criação do recipiente.

Se não for especificado um âmbito de encriptação padrão para o recipiente, então pode carregar uma bolha utilizando qualquer âmbito de encriptação que tenha definido para a conta de armazenamento. O âmbito de encriptação deve ser especificado no momento em que a bolha é carregada.

## <a name="disabling-an-encryption-scope"></a>Desativar um âmbito de encriptação

Quando desativar um âmbito de encriptação, quaisquer operações de leitura ou escrita subsequentes efetuadas com o âmbito de encriptação falharão com o código de erro HTTP 403 (Proibido). Se voltar a ativar o âmbito de encriptação, as operações de leitura e escrita voltarão a proceder normalmente.

Quando um âmbito de encriptação é desativado, já não é cobrado por isso. Desative quaisquer âmbitos de encriptação que não sejam necessários para evitar encargos desnecessários.

Se o seu âmbito de encriptação estiver protegido com uma chave gerida pelo cliente e eliminar a chave no cofre da chave, os dados tornar-se-ão inacessíveis. Certifique-se de também desativar o âmbito de encriptação para evitar ser carregado por ele.

Tenha em mente que as chaves geridas pelo cliente estão protegidas por proteção de eliminação e purga suave no cofre da chave, e uma chave eliminada está sujeita ao comportamento definido por essas propriedades. Para mais informações, consulte um dos seguintes tópicos na documentação do Cofre chave Azure:

- [Como utilizar soft-delete com PowerShell](../../key-vault/general/key-vault-recovery.md)
- [Como usar soft-delete com CLI](../../key-vault/general/key-vault-recovery.md)

> [!IMPORTANT]
> Não é possível eliminar um âmbito de encriptação.

## <a name="next-steps"></a>Passos seguintes

- [Azure Storage encryption for data at rest](../common/storage-service-encryption.md) (Encriptação do Armazenamento do Azure para dados inativos)
- [Criar e gerir âmbitos de encriptação](encryption-scope-manage.md)
- [Chaves geridas pelo cliente para encriptação de armazenamento Azure](../common/customer-managed-keys-overview.md)
- [O que é o Azure Key Vault?](../../key-vault/general/overview.md)