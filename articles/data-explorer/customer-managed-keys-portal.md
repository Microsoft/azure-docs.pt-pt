---
title: Configure as chaves geridas pelo cliente utilizando o portal Azure
description: Este artigo descreve como configurar a encriptação de chaves geridas pelo cliente nos seus dados no Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: itsagui
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: 180196f2c368207b76811700fd845406098600df
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529442"
---
# <a name="configure-customer-managed-keys-using-the-azure-portal"></a>Configure as chaves geridas pelo cliente utilizando o portal Azure

> [!div class="op_single_selector"]
> * [Portal](customer-managed-keys-portal.md)
> * [C #](customer-managed-keys-csharp.md)
> * [Modelo Azure Resource Manager](customer-managed-keys-resource-manager.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](../../includes/data-explorer-configure-customer-managed-keys.md)]

## <a name="enable-encryption-with-customer-managed-keys-in-the-azure-portal"></a>Ativar encriptação com chaves geridas pelo cliente no portal Azure

Este artigo mostra-lhe como ativar a encriptação de chaves geridas pelo cliente através do portal Azure. Por padrão, a encriptação do Azure Data Explorer utiliza chaves geridas pela Microsoft. Configure o seu cluster Azure Data Explorer para utilizar chaves geridas pelo cliente e especificar a chave para associar-se ao cluster.

1. No [portal Azure,](https://portal.azure.com/)aceda ao seu recurso de [cluster Azure Data Explorer.](create-cluster-database-portal.md#create-a-cluster) 
1. Selecione**Encriptação** **de Definições** > no painel esquerdo do portal.
1. No painel **de encriptação,** selecione **On** para a definição **de tecla gerida pelo Cliente.**
1. Clique em **Selecionar tecla**.

    ![Configurar chaves geridas pelo cliente](media/customer-managed-keys-portal/cmk-encryption-setting.png)

1. Na tecla Select da janela **Azure Key Vault,** selecione um **cofre chave** existente da lista de dropdown. Se selecionar **Criar novo** para criar um novo [Cofre](/azure/key-vault/quick-create-portal#create-a-vault)chave, será encaminhado para o ecrã Create **Key Vault.**

1. Selecione **Tecla**.
1. Selecione **versão**.
1. Clique em **Selecionar**.

    ![Selecione chave do Cofre de Chaves Azure](media/customer-managed-keys-portal/cmk-key-vault.png)

1. No painel **de encriptação** que agora contém a sua chave, selecione **Guardar**. Quando a criação da CMK for bem sucedida, verá uma mensagem de sucesso nas **Notificações.**

    ![Salvar a chave gerida pelo cliente](media/customer-managed-keys-portal/cmk-encryption-setting.png)

Ao ativar as chaves geridas pelo cliente para o seu cluster Azure Data Explorer, estará a criar um sistema de identidade atribuído ao cluster se não existir. Além disso, você estará fornecendo as permissões de get, wrapKey e unwarpKey necessárias para o seu cluster Azure Data Explorer no Cofre de Chaves selecionado e obter as propriedades do Cofre chave. 

> [!NOTE]
> Selecione **Off** para remover a chave gerida pelo cliente depois de ter sido criada.

## <a name="next-steps"></a>Passos seguintes

* [Clusters Secure Azure Data Explorer em Azure](security.md)
* [Proteja o seu cluster no Azure Data Explorer - portal Azure,](manage-cluster-security.md) permitindo a encriptação em repouso.
* [Configure as chaves geridas pelo cliente utilizando o modelo do Gestor de Recursos Azure](customer-managed-keys-resource-manager.md)
* [Configure as chaves geridas pelo cliente utilizando C #](customer-managed-keys-csharp.md)



