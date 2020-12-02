---
title: Integração do Cofre da Chave Azure com recurso Azure Stack Edge e ativação do dispositivo
description: Descreve como o Azure Key Vault está associado à gestão secreta durante a ativação do dispositivo Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: alkohli
ms.openlocfilehash: ec7a32739940d53d976e73a7e170df96a0acc245
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96449512"
---
# <a name="azure-key-vault-integration-with-azure-stack-edge"></a>Integração do Azure Key Vault com Azure Stack Edge 

O Azure Key Vault está integrado com o recurso Azure Stack Edge para gestão secreta. Este artigo fornece detalhes sobre como um Cofre de Chave Azure é criado para o recurso Azure Stack Edge durante a ativação do dispositivo e é então usado para gestão secreta. 


## <a name="about-key-vault-and-azure-stack-edge"></a>Sobre o cofre key e Azure Stack Edge

O serviço de nuvem Azure Key Vault é utilizado para armazenar e controlar de forma segura o acesso a fichas, senhas, certificados, chaves API e outros segredos. O Key Vault também facilita a criação e controlo das chaves de encriptação utilizadas para encriptar os seus dados. Para obter mais informações sobre transações permitidas e encargos correspondentes, consulte [preços para a Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).

Para o serviço Azure Stack Edge, um dos segredos usados é a Chave de Integridade do Canal (CIK). Esta chave permite-lhe encriptar os seus segredos. Com a integração do cofre chave, o CIK está guardado de forma segura no cofre da chave. Para mais informações, consulte [Securely store secrets and keys](../key-vault/general/overview.md#securely-store-secrets-and-keys).


## <a name="key-vault-creation"></a>Criação de cofre chave

Um cofre chave é criado para o recurso Azure Stack Edge durante o processo de geração de chaves de ativação. 

- Quando criar o recurso Azure Stack Edge, tem de registar o fornecedor de recursos *Microsoft.KeyVault.* O fornecedor de recursos é automaticamente registado se tiver acesso ao proprietário ou colaborador à subscrição. O cofre-chave é criado na mesma subscrição e no grupo de recursos que o recurso Azure Stack Edge. 

- Quando cria um recurso Azure Stack Edge, é também criado um Identidade de Serviço Gerido (MSI) que persiste durante o tempo de vida do recurso e comunica com o fornecedor de recursos na nuvem. 

    Quando o MSI está ativado, o Azure cria uma identidade fidedigna para o recurso Azure Stack Edge.

- Depois de ter criado o recurso Azure Stack Edge e gerar uma chave de ativação a partir do portal Azure, é criado um cofre-chave. Este cofre chave é usado para gestão secreta e persiste enquanto o recurso Azure Stack Edge existir. 

    ![Key Vault criado durante a geração de chaves de ativação](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-3.png)

- Pode optar por aceitar o nome de chave predefinido ou especificar um nome personalizado para o cofre de chaves. O nome do cofre deve ter entre 3 e 24 caracteres. Não pode usar um cofre que já está a ser utilizado. <!--The MSI is then used to authenticate to key vault to retrieve secrets.--> 

    ![MSI criado durante a criação de recursos Azure Stack Edge](media/azure-stack-edge-gpu-deploy-prep/create-resource-8.png)

- Para navegar no cofre da chave Azure, vá às **Propriedades** do seu recurso Azure Stack Edge e selecione o nome do cofre da chave. 

- Para evitar a eliminação acidental, está ativado um bloqueio de recursos no cofre da chave. Um soft-delete também é ativado no cofre da chave que permite que o cofre da chave seja restaurado dentro de 90 dias se houver uma eliminação acidental. Para obter mais informações, consulte [a visão geral do Azure Key Vault](../key-vault/general/soft-delete-overview.md)

    Se o cofre da chave for acidentalmente apagado e a duração da proteção da purga de 90 dias não tiver decorrido, siga estes passos para [recuperar o cofre da chave](../key-vault/general/key-vault-recovery.md#list-recover-or-purge-soft-deleted-secrets-keys-and-certificates). 

- Se tiver um recurso Azure Stack Edge existente antes do Cofre da Chave Azure ser integrado com o recurso Azure Stack Edge, não é afetado. Pode continuar a utilizar o seu recurso Azure Stack Edge existente. 

- Quando o seu recurso Azure Stack Edge é eliminado, o Cofre da Chave Azure também é eliminado com o recurso. Foi solicitado para confirmação. Se não pretender apagar este cofre de teclas, pode optar por não fornecer o seu consentimento. Apenas o recurso Azure Stack Edge é eliminado deixando o cofre intacto. 

- Se este cofre foi usado para armazenar outras chaves, então ainda pode restaurá-lo dentro de 90 dias após a eliminação. Durante esse período de proteção da purga, o nome do cofre não pode ser usado para criar um novo cofre.

Se encontrar problemas relacionados com a colocação de cofre e de ativação do dispositivo, consulte [problemas de ativação do dispositivo de resolução de problemas](azure-stack-edge-gpu-troubleshoot-activation.md).

<!--## Key vault secret management

When you generate an activation key, the following events occur:

1. You request an activation key in the Azure portal. The request is then sent to Key Vault resource provider. 
1. A standard tier key vault with access policy is created and is locked by default. This key vault uses the default name or the custom name that you specified.
1. The key vault authenticates with MSI the request to generate activation key. The MSI is also added to the key vault access policy and a channel integrity key is generated and placed in the key vault.
1. The activation key is returned to the Azure portal. You can then copy this key and use it in the local UI to activate your device.-->



## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre como gerar a [chave de ativação.](azure-stack-edge-gpu-deploy-prep.md#get-the-activation-key)