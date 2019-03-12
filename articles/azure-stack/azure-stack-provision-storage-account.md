---
title: Contas de armazenamento no Azure Stack | Documentos da Microsoft
description: Saiba como criar uma conta de armazenamento do Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 1/18/2019
ms.author: mabrigg
ms.lastreviewed: 1/18/2019
ms.openlocfilehash: 1d980ec1cac0ecf095adecd4eb6f22f7765ce11f
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57779136"
---
# <a name="storage-accounts-in-azure-stack"></a>Contas de armazenamento no Azure Stack
As contas de armazenamento incluem os serviços Blob e Tabela e o espaço de nomes exclusivo dos seus objetos de dados de armazenamento. Por predefinição, os dados na sua conta só estão disponíveis para si, o proprietário da conta de armazenamento.

1. No computador da prova de conceito do Azure Stack, inicie sessão no `https://adminportal.local.azurestack.external` como [administrador](azure-stack-connect-azure-stack.md)e, em seguida, clique em **+ criar um recurso** > **dados + armazenamento**  >  **Conta de armazenamento**.

   ![](media/azure-stack-provision-storage-account/image01.png)
2. Na **criar conta de armazenamento** painel, escreva um nome para a sua conta de armazenamento. Criar uma nova **grupo de recursos**, ou selecione um existente, em seguida, clique em **criar** para criar a conta de armazenamento.

   ![](media/azure-stack-provision-storage-account/image02.png)
3. Para ver a sua nova conta de armazenamento, clique em **todos os recursos**, em seguida, procure a conta de armazenamento e clique no respetivo nome.

    ![](media/azure-stack-provision-storage-account/image03.png)

### <a name="next-steps"></a>Passos Seguintes
[Utilizar modelos do Azure Resource Manager](user/azure-stack-arm-templates.md)

[Saiba mais sobre as contas de armazenamento do Azure](../storage/common/storage-create-storage-account.md)

[Baixe o guia de validação de armazenamento do Azure consistentes com o Azure Stack](https://aka.ms/azurestacktp1doc)
