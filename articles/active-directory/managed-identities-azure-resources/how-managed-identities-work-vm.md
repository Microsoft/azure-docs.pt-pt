---
title: Como identidades geridas para recursos Azure trabalham com máquinas virtuais Azure
description: Descrição das identidades geridas para os recursos Azure funcionam com máquinas virtuais Azure.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: conceptual
ms.custom: mvc
ms.date: 06/11/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: b93f45b05e6d7773afc2f750fd1a9a034c01ca1e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89178676"
---
# <a name="how-managed-identities-for-azure-resources-work-with-azure-virtual-machines"></a>Como identidades geridas para recursos Azure funcionam com máquinas virtuais Azure

Identidades geridas para recursos Azure fornecem aos serviços Azure uma identidade gerida automaticamente no Azure Ative Directory. Pode utilizar esta identidade para autenticar qualquer serviço que suporte a autenticação AZure AD, sem ter credenciais no seu código.

Neste artigo, aprende-se como as identidades geridas funcionam com máquinas virtuais Azure (VMs).


## <a name="how-it-works"></a>Como funciona

Internamente, as identidades geridas são princípios de serviço de um tipo especial, que só pode ser usado com recursos Azure. Quando a identidade gerida é eliminada, o ressiculante de serviço correspondente é automaticamente removido.
Além disso, quando um User-Assigned ou System-Assigned Identidade é criado, o Fornecedor de Recursos de Identidade Gerido (MSRP) emite um certificado internamente para essa identidade. 

O seu código pode utilizar uma identidade gerida para pedir tokens de acesso para serviços que suportem a autenticação do Azure AD. O Azure encarrega-se da implementação das credenciais que a instância do serviço utiliza. 

O diagrama seguinte mostra como é que as identidades de serviço geridas funcionam com as máquinas virtuais (VMs) do Azure:

![Identidades de Serviço Geridas e VMs do Azure](media/how-managed-identities-work-vm/data-flow.png)

|  Propriedade    | Identidade gerida atribuída pelo sistema | Identidade gerida atribuída pelo utilizador |
|------|----------------------------------|--------------------------------|
| Criação |  Criado como parte de um recurso Azure (por exemplo, uma máquina virtual Azure ou Serviço de Aplicações Azure). | Criado como um recurso autónomo da Azure. |
| Ciclo de vida | Ciclo de vida partilhado com o recurso Azure com o qual a identidade gerida é criada. <br/> Quando o recurso principal é eliminado, a identidade gerida também é eliminada. | Ciclo de vida independente. <br/> Deve ser explicitamente apagado. |
| Partilha através dos recursos Azure | Não pode ser partilhado. <br/> Só pode ser associado a um único recurso Azure. | Pode ser partilhado. <br/> A mesma identidade gerida atribuída pelo utilizador pode ser associada a mais de um recurso Azure. |
| Casos de utilização comuns | Cargas de trabalho contidas num único recurso Azure. <br/> Cargas de trabalho para as quais precisa de identidades independentes. <br/> Por exemplo, uma aplicação que funciona numa única máquina virtual | Cargas de trabalho que funcionam com múltiplos recursos e que podem partilhar uma única identidade. <br/> Cargas de trabalho que necessitam de pré-autorização para um recurso seguro como parte de um fluxo de provisionamento. <br/> Cargas de trabalho onde os recursos são reciclados frequentemente, mas as permissões devem manter-se consistentes. <br/> Por exemplo, uma carga de trabalho onde várias máquinas virtuais precisam de aceder ao mesmo recurso |

## <a name="system-assigned-managed-identity"></a>Identidade gerida atribuída pelo sistema

1. O Azure Resource Manager recebe um pedido para ativar a identidade gerida atribuída pelo sistema numa VM.

2. O Azure Resource Manager cria um principal de serviço no Azure AD para a identidade da VM. O principal de serviço é criado no inquilino do Azure AD no qual a subscrição confia.

3. O Azure Resource Manager configura a identidade no VM atualizando o ponto final de identidade do Serviço de Metadados de Azure Instance com o ID e certificado do cliente principal do serviço.

4. Quando a VM tiver uma identidade, utilize as informações do principal de serviço para lhe conceder acesso aos recursos do Azure. Para ligar para o Azure Resource Manager, utilize o controlo de acesso baseado em funções Azure (Azure RBAC) para atribuir o papel adequado ao diretor de serviço VM. Para chamar o Key Vault, conceda ao seu código acesso ao segredo ou à chave específica no Key Vault.

5. O seu código que está em execução no VM pode solicitar um token a partir do ponto final do serviço de metadados Azure Instance, acessível apenas a partir do VM: `http://169.254.169.254/metadata/identity/oauth2/token`
    - O parâmetro do recurso especifica o serviço para o qual o token é enviado. Para autenticar no Azure Resource Manager, utilize `resource=https://management.azure.com/`.
    - O parâmetro de versão da API especifica a versão do IMDS; utilize api-version=2018-02-01 ou superior.

6. É feita uma chamada para o Azure AD a pedir uma token de acesso (conforme especificado no passo 5) através da utilização do ID de cliente e do certificado configurados no passo 3. O Azure AD devolve um token de acesso JSON Web Token (JWT).

7. O código envia o token de acesso numa chamada para um serviço que suporte a autenticação do Azure AD.

## <a name="user-assigned-managed-identity"></a>Identidade gerida atribuída pelo utilizador

1. O Azure Resource Manager recebe um pedido para criar uma identidade gerida atribuída pelo utilizador.

2. O Azure Resource Manager cria um principal de serviço no Azure AD para a identidade gerida atribuída pelo utilizador. O principal de serviço é criado no inquilino do Azure AD no qual a subscrição confia.

3. O Azure Resource Manager recebe um pedido de configuração da identidade gerida atribuída pelo utilizador num VM e atualiza o ponto final de identidade do Serviço de Metadados de Ocorrência Azure com o ID e certificado principal do serviço de identidade gerido pelo utilizador.

4. Quando a identidade gerida atribuída pelo utilizador estiver criada, utilize as informações do principal de serviço para lhe conceder acesso aos recursos do Azure. Para ligar para o Azure Resource Manager, utilize o Azure RBAC para atribuir a função adequada ao principal de serviço da identidade atribuída ao utilizador. Para chamar o Key Vault, conceda ao seu código acesso ao segredo ou à chave específica no Key Vault.

   > [!Note]
   > Também pode concluir este passo antes do passo 3.

5. O seu código que está em execução no VM pode solicitar um token a partir do ponto final de identidade do Serviço de Metadados de Instância Azure, acessível apenas a partir do VM: `http://169.254.169.254/metadata/identity/oauth2/token`
    - O parâmetro do recurso especifica o serviço para o qual o token é enviado. Para autenticar no Azure Resource Manager, utilize `resource=https://management.azure.com/`.
    - O parâmetro de ID de cliente especifica a identidade para a qual o token é pedido. Este valor é necessário para eliminar ambiguidades se uma única VM tiver mais de uma identidade atribuída pelo utilizador.
    - O parâmetro da versão da API especifica a versão do Azure Instance Metadata Service. Utilize `api-version=2018-02-01` ou superior.

6. É feita uma chamada para o Azure AD a pedir uma token de acesso (conforme especificado no passo 5) através da utilização do ID de cliente e do certificado configurados no passo 3. O Azure AD devolve um token de acesso JSON Web Token (JWT).
7. O código envia o token de acesso numa chamada para um serviço que suporte a autenticação do Azure AD.


## <a name="next-steps"></a>Passos seguintes

Comece a utilizar a funcionalidade de identidades geridas para recursos do Azure com os inícios rápidos seguintes:

* [Utilizar uma identidade gerida atribuída pelo sistema de VM do Windows para aceder ao Resource Manager](tutorial-windows-vm-access-arm.md)
* [Utilizar uma identidade gerida atribuída pelo sistema de VM do Linux para aceder ao Resource Manager](tutorial-linux-vm-access-arm.md)
