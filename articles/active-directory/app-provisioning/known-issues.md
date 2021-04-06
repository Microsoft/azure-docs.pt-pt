---
title: Questões conhecidas para provisionamento de aplicações em Azure AD
description: Conheça questões conhecidas ao trabalhar com o provisionamento de aplicações automatizadas em Azure AD.
author: kenwith
ms.author: kenwith
manager: daveba
services: active-directory
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 01/05/2021
ms.reviewer: arvinh
ms.openlocfilehash: 9eba671f6c824c8c88388f2b9d61512dfb1d122f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99256648"
---
# <a name="known-issues-application-provisioning"></a>Questões conhecidas: Provisionamento de pedidos
Problemas conhecidos a ter em atenção ao trabalhar com o aprovisionamento de aplicações. Pode fornecer feedback sobre o serviço de fornecimento de aplicações no UserVoice, ver [Azure AD Application Provision UserVoice](https://aka.ms/appprovisioningfeaturerequest). Assistimos atentamente ao UserVoice para que possamos melhorar o serviço. 

> [!NOTE]
> Esta não é uma lista completa de assuntos conhecidos. Se souber de um problema que não está listado, forneça feedback na parte inferior da página.

## <a name="authorization"></a>Autorização 

**Incapaz de guardar após teste de ligação bem sucedido**

Se conseguir testar uma ligação com sucesso, mas não puder economizar, então excedeu o limite de armazenamento permitido para credenciais. Para saber mais, consulte [as credenciais de administrador de poupança de problemas](./user-provisioning.md).

**Incapaz de salvar**

A URL do inquilino, o símbolo secreto e o e-mail de notificação devem ser preenchidos para economizar. Não pode fornecer apenas um deles. 

**Incapaz de alterar o modo de provisionamento de volta ao manual**

Depois de configurar o provisionamento pela primeira vez, irá notar que o modo de provisionamento mudou de manual para automático. Não pode mudá-lo de volta para manual. Mas podes desligar o fornecimento através da UI. Desligar o fornecimento na UI faz o mesmo que ajustar o dropdown manualmente.  


## <a name="attribute-mappings"></a>Mapeamentos de atributos 

**Atributo SamAccountName ou userType não disponível como atributo de origem**

Os atributos SamAccountName e userType não estão disponíveis como um atributo de origem por padrão. Estenda o seu esquema para adicionar o atributo. Pode adicionar os atributos à lista de atributos de origem disponíveis, alargando o seu esquema. Para saber mais, consulte [atributo de origem em falta.](user-provisioning-sync-attributes-for-mapping.md) 

**Queda de atributo de origem em falta para extensão de esquema**

As extensões ao seu esquema podem por vezes faltar a partir do atributo de origem que desce na UI. Entre nas definições avançadas dos mapeamentos do seu atributo e adicione manualmente os atributos. Para saber mais, consulte [Personalizar atribuir mapeamentos de atributos.](customize-application-attributes.md)

**Atributo nulo não pode ser provisionado**

A AZure AD não pode atualmente providenciar atributos nulos. Se um atributo for nulo no objeto do utilizador, este será ignorado. 

**Caracteres max para expressões de mapeamento de atributos**

As expressões de mapeamento de atributos podem ter um máximo de 10.000 caracteres. 

**Filtros de deteção não suportados**

Extensões de diretório, appRoleAssignments, userType e accountExpires não são suportados como filtros de escoamento.


## <a name="service-issues"></a>Problemas de serviço 

**Cenários não suportados**

- O fornecimento de senhas não é suportado. 
- O provisionamento de grupos aninhados não é apoiado. 
- O fornecimento aos inquilinos B2C não é suportado devido ao tamanho dos inquilinos.
- Nem todas as aplicações de provisionamento estão disponíveis em todas as nuvens. Por exemplo, o Atlassian ainda não está disponível na Nuvem do Governo. Estamos a trabalhar com programadores de aplicações para embarcar nas suas aplicações para todas as nuvens.

**O fornecimento automático não está disponível na minha aplicação baseada no OIDC**

Se criar um registo de aplicações, o correspondente principal de serviço em aplicações empresariais não será habilitado para o fornecimento automático do utilizador. Será necessário solicitar que a aplicação seja adicionada à galeria, se for destinada a ser utilizada por várias organizações, ou criar uma segunda aplicação não-galeria para o provisionamento. 

**O intervalo de provisionamento é fixado**

O [tempo](./application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users) entre os ciclos de provisionamento não é atualmente configurável. 

**Alterações que não se movem da app-alvo para Azure AD**

O serviço de fornecimento de aplicações não está ciente das alterações feitas em aplicações externas. Portanto, não são tomadas medidas para recuar. O serviço de fornecimento de aplicações baseia-se em alterações feitas no Azure AD. 

**Mudar de sincronização para sincronização não funcionando**

Depois de alterar o âmbito de 'Sync All' para 'Sync Assigned', certifique-se de que também efetua um reinício para garantir que a alteração entra em vigor. Pode fazer o recomeço da UI.

**Ciclo de provisionamento continua até à conclusão**

Ao definir o provisionamento `enabled = off` , ou bater no batente, o ciclo de provisionamento atual continuará a funcionar até à conclusão. O serviço deixará de executar quaisquer ciclos futuros até voltar a ligar o provisionamento.

**Membro do grupo não previsto**

Quando um grupo estiver no âmbito e um membro estiver fora de alcance, o grupo será provisionado. O utilizador fora de alcance não será a provisionado. Se o membro voltar ao alcance, o serviço não detetará imediatamente a alteração. O reinício do provisionamento abordará a questão. Recomendamos reiniciar periodicamente o serviço para garantir que todos os utilizadores estão devidamente abastados.  

**O gestor não está previsto.**

Se um utilizador e o seu gestor estiverem ambos em possibilidade de fornecimento, o serviço irá prestar o utilizador e, em seguida, atualizar o gestor. No entanto, se no primeiro dia o utilizador estiver no âmbito e o gestor estiver fora de alcance, providenciaremos o utilizador sem a referência do gestor. Quando o gestor entrar em campo, a referência do gestor não será atualizada até reiniciar o fornecimento e fazer com que o serviço volte a avaliar todos os utilizadores. 

## <a name="next-steps"></a>Passos seguintes
- [Como funciona o aprovisionamento](how-provisioning-works.md)
