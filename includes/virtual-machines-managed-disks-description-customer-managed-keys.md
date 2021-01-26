---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: ba50def51bcea4f477bea5cecbe5b1ed0409b01a
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/25/2021
ms.locfileid: "98792403"
---
Pode optar por gerir a encriptação ao nível de cada disco gerido, com as suas próprias chaves. A encriptação do lado do servidor para discos geridos com chaves geridas pelo cliente oferece uma experiência integrada com o Azure Key Vault. Pode importar [as chaves RSA](../articles/key-vault/keys/hsm-protected-keys.md) para o cofre de chaves ou gerar novas chaves RSA no Cofre da Chave Azure. 

Os discos geridos pela Azure tratam a encriptação e a desencriptação de forma totalmente transparente utilizando [a encriptação do envelope.](../articles/storage/common/storage-client-side-encryption.md#encryption-and-decryption-via-the-envelope-technique) Encripta dados utilizando uma chave de encriptação de dados baseada em [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256 (DEK), que está, por sua vez, protegida usando as suas chaves. O serviço de armazenamento gera chaves de encriptação de dados e encripta-as com chaves geridas pelo cliente utilizando encriptação RSA. A encriptação do envelope permite-lhe rodar (alterar) as suas chaves periodicamente de acordo com as suas políticas de conformidade sem afetar os seus VMs. Quando gira as suas chaves, o serviço de Armazenamento reencripta as chaves de encriptação de dados com as novas teclas geridas pelo cliente. 

#### <a name="full-control-of-your-keys"></a>Controlo total das suas chaves

Tem de conceder acesso a discos geridos no cofre para usar as suas chaves para encriptar e desencriptar o DEK. Isto permite-lhe o controlo total dos seus dados e chaves. Pode desativar as suas chaves ou revogar o acesso a discos geridos a qualquer momento. Também pode auditar a utilização da chave de encriptação com a monitorização do Azure Key Vault para garantir que apenas discos geridos ou outros serviços de Azure confiáveis estão a aceder às suas chaves.

Para SSDs premium, SSDs standard e HDDs padrão: Quando desativar ou apagar a sua chave, quaisquer VMs com discos que utilizem essa tecla serão automaticamente desligados. Depois disso, os VM não serão utilizáveis a menos que a chave esteja novamente ativada ou atribua uma nova chave.    

Para discos ultra: quando desativar ou apagar uma tecla, quaisquer VMs com discos ultra usando a tecla não desligam automaticamente. Assim que fizer negócios e reiniciar os VMs, os discos deixarão de usar a chave e os VM não voltarão a funcionar. Para repor os VMs, tem de atribuir uma nova chave ou ativar a chave existente.    

O seguinte diagrama mostra como os discos geridos utilizam o Azure Ative Directory e o Azure Key Vault para fazer pedidos utilizando a chave gerida pelo cliente:

:::image type="content" source="media/virtual-machines-managed-disks-description-customer-managed-keys/customer-managed-keys-sse-managed-disks-workflow.png" alt-text="Fluxo de trabalho de chaves gerido pelo disco gerido pelo cliente e gerido pelo cliente. Um administrador cria um Cofre de Chave Azure, em seguida, cria um conjunto de encriptação de disco e configura o conjunto de encriptação do disco. O conjunto está associado a um VM, que permite ao disco fazer uso da AD Azure para autenticar":::

A seguinte lista explica o diagrama com mais detalhes:

1. Um administrador do Azure Key Vault cria recursos de cofre chave.
1. O administrador do cofre-chave importa as chaves RSA para o Key Vault ou gera novas chaves RSA no Key Vault.
1. Este administrador cria uma instância de recurso de Conjunto de Encriptação de Disco, especificando um ID do cofre da chave Azure e um URL chave. O Conjunto de Encriptação de Disco é um novo recurso introduzido para simplificar a gestão chave para discos geridos. 
1. Quando um conjunto de encriptação de disco é criado, uma [identidade gerida atribuída ao sistema](../articles/active-directory/managed-identities-azure-resources/overview.md) é criada no Azure Ative Directory (AD) e associada ao conjunto de encriptação do disco. 
1. O administrador do cofre-chave Azure concede então a permissão de identidade gerida para realizar operações no cofre principal.
1. Um utilizador VM cria discos associando-os ao conjunto de encriptação do disco. O utilizador VM também pode ativar a encriptação do lado do servidor com as chaves geridas pelo cliente para os recursos existentes, associando-os ao conjunto de encriptação do disco. 
1. Os discos geridos utilizam a identidade gerida para enviar pedidos para o Cofre da Chave Azure.
1. Para ler ou escrever dados, os discos geridos enviam pedidos ao Azure Key Vault para encriptar (embrulhar) e desencriptar (desembrulhar) a chave de encriptação de dados de forma a realizar encriptação e desencriptação dos dados. 

Para revogar o acesso às chaves geridas pelo cliente, consulte [o Azure Key Vault PowerShell](/powershell/module/azurerm.keyvault/) e [o Azure Key Vault CLI](/cli/azure/keyvault). A revogação do acesso bloqueia eficazmente o acesso a todos os dados na conta de armazenamento, uma vez que a chave de encriptação é inacessível pela Azure Storage.
