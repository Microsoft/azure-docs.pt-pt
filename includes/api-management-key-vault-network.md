---
author: dlepow
ms.service: api-management
ms.topic: include
ms.date: 01/26/2021
ms.author: danlep
ms.openlocfilehash: a9dbedd8516f3a3a592c7fd4f4f5563011d6c6db
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99491021"
---
#### <a name="requirements-for-key-vault-firewall"></a>Requisitos para firewall Key Vault

Se [a firewall key Vault](../articles/key-vault/general/network-security.md) estiver ativada no cofre da chave, os seguintes são requisitos adicionais:

* Deve utilizar a identidade gerida **do sistema atribuído à** API Management para aceder ao cofre de chaves.
* Na firewall Key Vault, permita que os **Serviços Da Microsoft fidedignas contornem esta** opção de firewall.

#### <a name="virtual-network-requirements"></a>Requisitos de rede virtual

Se a instância de Gestão da API for implantada numa rede virtual, também configurar as seguintes definições de rede:

* Ativar um [ponto final](../articles/key-vault/general/overview-vnet-service-endpoints.md) de serviço para a Azure Key Vault na sub-rede API Management.
* Configure uma regra do grupo de segurança da rede (NSG) para permitir o tráfego de saída para as [etiquetas](../articles/virtual-network/service-tags-overview.md)de serviço AzureKeyVault e AzureActiveDirectory . 

Para mais detalhes, consulte os detalhes da configuração da rede no [Connect a uma rede virtual.](../articles/api-management/api-management-using-with-vnet.md#-common-network-configuration-issues)