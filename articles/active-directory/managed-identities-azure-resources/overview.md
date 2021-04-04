---
title: Identidades geridas para os recursos do Azure
description: Descrição geral das identidades geridas para os recursos do Azure.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: overview
ms.custom: mvc
ms.date: 10/06/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 728ca38cc3ef3bf989a75d757c69f7ca1993d82d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91803126"
---
# <a name="what-are-managed-identities-for-azure-resources"></a>Quais são as identidades geridas para os recursos do Azure?

Um desafio comum para os desenvolvedores é a gestão de segredos e credenciais para garantir a comunicação entre diferentes serviços. No Azure, identidades geridas eliminam a necessidade de os desenvolvedores terem de gerir credenciais fornecendo uma identidade para o recurso Azure em Azure AD e usando-o para obter fichas do Azure Ative Directory (Azure AD). Isto também ajuda a aceder ao [Azure Key Vault](../../key-vault/general/overview.md) onde os desenvolvedores podem armazenar credenciais de forma segura. Identidades geridas para recursos Azure resolvem este problema fornecendo aos serviços Azure uma identidade gerida automaticamente em Azure AD.

Para que pode ser usada uma identidade gerida?

   > [!VIDEO https://www.youtube.com/embed/5lqayO_oeEo]

Aqui estão alguns dos benefícios da utilização de identidades geridas:

- Não precisas de gerir credenciais. As credenciais nem sequer são acessíveis a si.
- Pode utilizar identidades geridas para autenticar qualquer serviço Azure que suporte a autenticação AD Azure, incluindo o Cofre da Chave Azure.
- As identidades geridas podem ser utilizadas sem qualquer custo adicional.

> [!NOTE]
> Identidades geridas para recursos do Azure é o novo nome para o serviço anteriormente conhecido como Identidade de Serviço Gerida (MSI).

## <a name="managed-identity-types"></a>Tipos de identidade geridos

Existem dois tipos de identidades geridas:

- **Afetado pelo sistema** Alguns serviços da Azure permitem-lhe ativar uma identidade gerida diretamente numa instância de serviço. Quando ativa uma identidade gerida atribuída ao sistema, uma identidade é criada em Azure AD que está ligada ao ciclo de vida dessa instância de serviço. Assim, quando o recurso é eliminado, o Azure elimina automaticamente a identidade para si. Por design, apenas esse recurso Azure pode usar esta identidade para solicitar fichas da Azure AD.
- **Atribuído pelo utilizador** Também pode criar uma identidade gerida como um recurso autónomo do Azure. Pode [criar uma identidade gerida atribuída pelo utilizador](how-to-manage-ua-identity-portal.md) e atribuí-la a um ou mais casos de um serviço Azure. No caso de identidades geridas atribuídas pelo utilizador, a identidade é gerida separadamente dos recursos que a utilizam. </br></br>

  > [!VIDEO https://www.youtube.com/embed/OzqpxeD3fG0]

A tabela abaixo mostra as diferenças entre os dois tipos de identidades geridas.

|  Propriedade    | Identidade gerida atribuída pelo sistema | Identidade gerida atribuída pelo utilizador |
|------|----------------------------------|--------------------------------|
| Criação |  Criado como parte de um recurso Azure (por exemplo, uma máquina virtual Azure ou Serviço de Aplicações Azure) | Criado como um recurso autónomo da Azure |
| Ciclo de vida | Ciclo de vida partilhado com o recurso Azure com o qual a identidade gerida é criada. <br/> Quando o recurso principal é eliminado, a identidade gerida também é eliminada. | Ciclo de vida independente. <br/> Deve ser explicitamente apagado. |
| Partilha através dos recursos Azure | Não pode ser partilhado. <br/> Só pode ser associado a um único recurso Azure. | Pode ser partilhado <br/> A mesma identidade gerida atribuída pelo utilizador pode ser associada a mais de um recurso Azure. |
| Casos de utilização comuns | Cargas de trabalho contidas num único recurso Azure <br/> Cargas de trabalho para as quais precisa de identidades independentes. <br/> Por exemplo, uma aplicação que funciona numa única máquina virtual | Cargas de trabalho que funcionam com múltiplos recursos e que podem partilhar uma única identidade. <br/> Cargas de trabalho que necessitam de pré-autorização para um recurso seguro como parte de um fluxo de provisionamento. <br/> Cargas de trabalho onde os recursos são reciclados frequentemente, mas as permissões devem manter-se consistentes. <br/> Por exemplo, uma carga de trabalho onde várias máquinas virtuais precisam de aceder ao mesmo recurso |

>[!IMPORTANT]
>Independentemente do tipo de identidade escolhida, uma identidade gerida é um diretor de serviço de um tipo especial que só pode ser utilizado com recursos Azure. Quando a identidade gerida é eliminada, o ressiculante de serviço correspondente é automaticamente removido.

## <a name="how-can-i-use-managed-identities-for-azure-resources"></a>Como posso utilizar as identidades geridas para os recursos do Azure?

![alguns exemplos de como um desenvolvedor pode usar identidades geridas para obter acesso aos recursos a partir do seu código sem gerir informações de autenticação](media/overview/azure-managed-identities-examples.png)

## <a name="what-azure-services-support-the-feature"></a>Que serviços do Azure suportam a funcionalidade?<a name="which-azure-services-support-managed-identity"></a>

As identidades geridas para recurso do Azure podem ser utilizadas para autenticação em serviços que suportem a autenticação do Azure AD. Para obter uma lista dos serviços do Azure que suportam a funcionalidade de identidades geridas para recursos do Azure, veja [Serviços que suportam as identidades geridas para recursos do Azure](./services-support-managed-identities.md).

## <a name="next-steps"></a>Passos seguintes

* [Utilizar uma identidade gerida atribuída pelo sistema de VM do Windows para aceder ao Resource Manager](tutorial-windows-vm-access-arm.md)
* [Utilizar uma identidade gerida atribuída pelo sistema de VM do Linux para aceder ao Resource Manager](tutorial-linux-vm-access-arm.md)
* [Como utilizar identidades geridas para o Serviço de Aplicações e Funções Azure](../../app-service/overview-managed-identity.md)
* [Como utilizar identidades geridas com o Azure Container Instances](../../container-instances/container-instances-managed-identity.md)
* [Implementação de identidades geridas para os Recursos Azure da Microsoft.](https://www.pluralsight.com/courses/microsoft-azure-resources-managed-identities-implementing)