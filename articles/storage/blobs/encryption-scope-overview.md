---
title: Âmbitos de encriptação para armazenamento blob (pré-visualização)
description: Os âmbitos de encriptação fornecem a capacidade de gerir a encriptação ao nível do recipiente ou de uma bolha individual. Pode utilizar âmbitos de encriptação para criar limites seguros entre dados que residem na mesma conta de armazenamento, mas que pertencem a diferentes clientes.
services: storage
author: tamram
ms.service: storage
ms.date: 03/05/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 35395a30f7d58b9edb3aa7622a35e8c4a62dc76f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102211367"
---
# <a name="encryption-scopes-for-blob-storage-preview"></a>Âmbitos de encriptação para armazenamento blob (pré-visualização)

Os âmbitos de encriptação fornecem a capacidade de gerir a encriptação ao nível do recipiente ou de uma bolha individual. Pode utilizar âmbitos de encriptação para criar limites seguros entre dados que residem na mesma conta de armazenamento, mas que pertencem a diferentes clientes.

Por padrão, uma conta de armazenamento é encriptada com uma chave que é telescópio em toda a conta de armazenamento. Com um âmbito de encriptação, pode especificar que um ou mais contentores são encriptados com uma chave que é apenas traçada para esses contentores.

Pode optar por utilizar as teclas geridas pela Microsoft ou as teclas geridas pelo cliente armazenadas no Cofre da Chave Azure para proteger e controlar o acesso à chave que encripta os seus dados. Diferentes âmbitos de encriptação na mesma conta de armazenamento podem utilizar chaves geridas pela Microsoft ou geridas pelo cliente.

Depois de ter criado um âmbito de encriptação, pode especificar esse âmbito de encriptação num pedido de criação de um recipiente ou de uma bolha. Para obter mais informações sobre como criar um âmbito de encriptação, consulte [Criar e gerir os âmbitos de encriptação (pré-visualização)](encryption-scope-manage.md).

> [!IMPORTANT]
> Os âmbitos de encriptação estão atualmente em **PREVIEW**. Consulte os [Termos Complementares de Utilização para o Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para termos legais aplicáveis às funcionalidades do Azure que estejam em versão beta, pré-visualização ou ainda não lançadas em disponibilidade geral.
>
> Para evitar custos inesperados, certifique-se de desativar quaisquer âmbitos de encriptação que não necessita atualmente.
>
> Os âmbitos de encriptação não são suportados com armazenamento geo-redundante de acesso à leitura (RA-GRS) ou contas de armazenamento redundante de zona de acesso à leitura (RA-GZRS) durante a pré-visualização.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="create-a-container-or-blob-with-an-encryption-scope"></a>Criar um recipiente ou bolha com um âmbito de encriptação

As bolhas criadas sob um âmbito de encriptação são encriptadas com a chave especificada para esse âmbito. Pode especificar um âmbito de encriptação para uma bolha individual quando criar a bolha, ou pode especificar um âmbito de encriptação padrão quando criar um recipiente. Quando um âmbito de encriptação predefinido é especificado ao nível de um recipiente, todas as bolhas nesse recipiente são encriptadas com a chave associada ao âmbito padrão.

Quando cria uma bolha num recipiente que tenha um âmbito de encriptação padrão, pode especificar um âmbito de encriptação que substitui o âmbito de encriptação padrão se o recipiente estiver configurado para permitir substituições do âmbito de encriptação padrão. Para evitar sobreposições do âmbito de encriptação padrão, configuure o recipiente para negar sobreposições para uma bolha individual.

A leitura das operações numa bolha que pertence a um âmbito de encriptação acontece de forma transparente, desde que o âmbito de encriptação não seja desativado.

## <a name="disable-an-encryption-scope"></a>Desativar um âmbito de encriptação

Quando desativar um âmbito de encriptação, quaisquer operações de leitura ou escrita subsequentes efetuadas com o âmbito de encriptação falharão com o código de erro HTTP 403 (Proibido). Se voltar a ativar o âmbito de encriptação, as operações de leitura e escrita voltarão a proceder normalmente.

Quando um âmbito de encriptação é desativado, já não é cobrado por isso. Desative quaisquer âmbitos de encriptação que não sejam necessários para evitar encargos desnecessários.

Se o seu âmbito de encriptação estiver protegido com teclas geridas pelo cliente, também pode eliminar a chave associada no cofre de teclas para desativar o âmbito de encriptação. Tenha em mente que as chaves geridas pelo cliente estão protegidas por proteção de eliminação e purga suave no cofre da chave, e uma chave eliminada está sujeita ao comportamento definido por essas propriedades. Para mais informações, consulte um dos seguintes tópicos na documentação do Cofre chave Azure:

- [Como utilizar soft-delete com PowerShell](../../key-vault/general/key-vault-recovery.md)
- [Como usar soft-delete com CLI](../../key-vault/general/key-vault-recovery.md)

> [!NOTE]
> Não é possível eliminar um âmbito de encriptação.

## <a name="next-steps"></a>Passos seguintes

- [Azure Storage encryption for data at rest](../common/storage-service-encryption.md) (Encriptação do Armazenamento do Azure para dados inativos)
- [Criar e gerir os âmbitos de encriptação (pré-visualização)](encryption-scope-manage.md)
- [Chaves geridas pelo cliente para encriptação de armazenamento Azure](../common/customer-managed-keys-overview.md)
- [O que é o Azure Key Vault?](../../key-vault/general/overview.md)