---
title: Proteja o seu cluster no Azure Data Explorer
description: Este artigo descreve como proteger o seu cluster no Azure Data Explorer dentro do portal Azure.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 0f935999b68a7283c032d43c42d688b273d5c450
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75720349"
---
# <a name="secure-your-cluster-in-azure-data-explorer---azure-portal"></a>Proteja o seu cluster no Azure Data Explorer - Portal Azure

[A Encriptação do Disco Azure](/azure/security/azure-security-disk-encryption-overview) ajuda a proteger e salvaguardar os seus dados para cumprir os seus compromissos de segurança organizacional e conformidade. Fornece encriptação de volume para o OS e discos de dados das suas máquinas virtuais cluster. Integra-se também com o [Azure Key Vault,](/azure/key-vault/)que nos permite controlar e gerir as chaves e segredos de encriptação do disco, e garantir que todos os dados nos discos VM estão encriptados. 
  
## <a name="enable-encryption-at-rest-in-the-azure-portal"></a>Ativar encriptação em repouso no portal Azure
  
As definições de segurança do cluster permitem ativar a encriptação do disco no seu cluster. Permitir a [encriptação em repouso](/azure/security/fundamentals/encryption-atrest) no seu cluster fornece proteção de dados para dados armazenados (em repouso). 

1. No portal Azure, aceda ao seu recurso de cluster Azure Data Explorer. Sob a rubrica **Definições,** selecione **Security**. 

    ![Ligue a encriptação em repouso](media/manage-cluster-security/security-encryption-at-rest.png)

1. Na janela **De Segurança,** selecione **On** para a definição de segurança de **encriptação do disco.** 

1. Selecione **Guardar**.
 
> [!NOTE]
> Selecione **Off** para desativar a encriptação depois de ativada.

## <a name="next-steps"></a>Passos seguintes

[Verificar estado de funcionamento do cluster](/azure/data-explorer/check-cluster-health)
