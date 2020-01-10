---
title: Proteger seu cluster no Azure Data Explorer
description: Este artigo descreve como proteger seu cluster no Azure Data Explorer no portal do Azure.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 0f935999b68a7283c032d43c42d688b273d5c450
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/07/2020
ms.locfileid: "75720349"
---
# <a name="secure-your-cluster-in-azure-data-explorer---azure-portal"></a>Proteger seu cluster no Azure Data Explorer-portal do Azure

[Azure Disk Encryption](/azure/security/azure-security-disk-encryption-overview) ajuda a proteger e proteger seus dados para atender aos compromissos de segurança e conformidade da organização. Ele fornece criptografia de volume para o sistema operacional e discos de dados de suas máquinas virtuais de cluster. Ele também se integra com [Azure Key Vault](/azure/key-vault/), que nos permite controlar e gerenciar as chaves e os segredos de criptografia de disco e garantir que todos os dados nos discos de VM sejam criptografados. 
  
## <a name="enable-encryption-at-rest-in-the-azure-portal"></a>Habilitar a criptografia em repouso no portal do Azure
  
As configurações de segurança do cluster permitem habilitar a criptografia de disco no cluster. Habilitar a [criptografia em repouso](/azure/security/fundamentals/encryption-atrest) no cluster fornece proteção de dados para dados armazenados (em repouso). 

1. Na portal do Azure, vá para o recurso de cluster de Data Explorer do Azure. No título **configurações** , selecione **segurança**. 

    ![Ativar a criptografia em repouso](media/manage-cluster-security/security-encryption-at-rest.png)

1. Na janela **segurança** , selecione **ativado** para a configuração de segurança de **criptografia de disco** . 

1. Selecione **Guardar**.
 
> [!NOTE]
> Selecione **desativado** para desabilitar a criptografia depois que ela tiver sido habilitada.

## <a name="next-steps"></a>Passos seguintes

[Verificar integridade do cluster](/azure/data-explorer/check-cluster-health)
