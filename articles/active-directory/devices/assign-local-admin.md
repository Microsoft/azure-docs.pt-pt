---
title: Como gerir administradores locais em dispositivos aderes ao Azure AD
description: Saiba como atribuir funções ao Azure ao grupo de administradores locais de um dispositivo Windows.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: dc1812d955590ec0c7372e1311c9d69f93b9957c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128887"
---
# <a name="how-to-manage-the-local-administrators-group-on-azure-ad-joined-devices"></a>Como gerir o grupo de administradores locais em Azure AD aderiu a dispositivos

Para gerir um dispositivo Windows, é necessário ser membro do grupo de administradores locais. Como parte do processo de adesão do Azure Ative Directory (Azure AD), a Azure AD atualiza a adesão deste grupo a um dispositivo. Pode personalizar a atualização de adesão para satisfazer os seus requisitos de negócio. Uma atualização de membros é, por exemplo, útil se pretender permitir que o seu pessoal de helpdesk faça tarefas que requerem direitos de administrador num dispositivo.

Este artigo explica como funciona a atualização de adesão e como pode personalizá-la durante um Azure AD Join. O conteúdo deste artigo não se aplica a um anúncio **híbrido** azure.

## <a name="how-it-works"></a>Como funciona

Quando liga um dispositivo Windows com a Azure AD utilizando uma adesão ao Azure AD, o Azure AD adiciona os seguintes princípios de segurança ao grupo de administradores locais no dispositivo:

- O papel de administrador global da AD Azure
- A função de administrador de dispositivos Azure AD 
- O utilizador que executa o Azure AD junta-se   

Ao adicionar funções de AD Azure ao grupo de administradores locais, pode atualizar os utilizadores que podem gerir um dispositivo a qualquer momento no Azure AD sem modificar nada no dispositivo. Atualmente, não é possível atribuir grupos a um papel de administrador.
A Azure AD também adiciona o papel de administrador de dispositivos Azure AD ao grupo de administradores locais para apoiar o princípio do menor privilégio (PoLP). Além dos administradores globais, também pode ativar utilizadores que tenham sido atribuídos *apenas* a função de administrador de dispositivopara gerir um dispositivo. 

## <a name="manage-the-global-administrators-role"></a>Gerir o papel de administradores globais

Para visualizar e atualizar a adesão ao papel de administrador global, consulte:

- [Ver todos os membros de um papel de administrador no Diretório Ativo Azure](../users-groups-roles/directory-manage-roles-portal.md)
- [Atribua um utilizador a funções de administrador no Azure Active Directory](../fundamentals/active-directory-users-assign-role-azure-portal.md)


## <a name="manage-the-device-administrator-role"></a>Gerir a função de administrador do dispositivo 

No portal Azure, pode gerir a função de administrador de dispositivos na página **dispositivos.** Para abrir a página **Dispositivos:**

1. Inscreva-se no seu [portal Azure](https://portal.azure.com) como administrador global.
1. Procure e selecione *Azure Active Directory*.
1. Na secção **Gerir,** clique em **Dispositivos**.
1. Na página **Dispositivos,** clique nas **definições do Dispositivo**.

Para modificar a função de administrador do dispositivo, configure **administradores locais adicionais em dispositivos ligados ao Azure AD**.  

![Administradores locais adicionais](./media/assign-local-admin/10.png)

>[!NOTE]
> Esta opção requer um inquilino Azure AD Premium. 

Os administradores de dispositivos são atribuídos a todos os dispositivos azure AD. Não é possível examinar os administradores de dispositivos para um conjunto específico de dispositivos. Atualizar a função de administrador do dispositivo não tem necessariamente um impacto imediato nos utilizadores afetados. Nos dispositivos em que um utilizador já está assinado, a atualização de privilégios ocorre quando *ambas as* ações abaixo acontecem:

- Passaram-se 4 horas para que a AD Azure emita um novo Token De Refresco Primário com os privilégios apropriados. 
- O utilizador assina e volta a entrar, não bloqueia/desbloqueia, para refrescar o seu perfil.

## <a name="manage-regular-users"></a>Gerir utilizadores regulares

Por predefinição, a Azure AD adiciona ao utilizador que executa o AD Azure a juntar-se ao grupo de administradores no dispositivo. Se pretender evitar que utilizadores regulares se tornem administradores locais, tem as seguintes opções:

- [Windows Autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot) - O Windows Autopilot oferece-lhe uma opção para evitar que o utilizador primário realize a adesão a um administrador local. Pode fazê-lo [criando um perfil Autopilot](/intune/enrollment-autopilot#create-an-autopilot-deployment-profile).
- [Inscrição](/intune/windows-bulk-enroll) a granel - Uma adesão a Azure AD que é realizada no contexto de uma inscrição a granel acontece no contexto de um utilizador criado automaticamente. Os utilizadores que saem depois de um dispositivo ter sido aderido não são adicionados ao grupo de administradores.   

## <a name="manually-elevate-a-user-on-a-device"></a>Elevar manualmente um utilizador num dispositivo 

Além de utilizar o processo de adesão ao Azure AD, também pode elevar manualmente um utilizador regular para se tornar um administrador local num dispositivo específico. Este passo requer que já seja membro do grupo de administradores locais. 

A partir do lançamento **do Windows 10 1709,** pode executar esta tarefa a partir de **Definições -> Contas -> Outros utilizadores**. **Selecione Adicionar um trabalho ou utilizador escolar,** insira a UPN do utilizador sob conta de **utilizador** e selecione *Administrador* sob o tipo de **conta**  
 
Além disso, também pode adicionar utilizadores usando o pedido de comando:

- Se os utilizadores dos seus inquilinos estiverem sincronizados `net localgroup administrators /add "Contoso\username"`a partir do Diretório Ativo no local, utilize .
- Se os utilizadores do seu inquilino forem criados em Azure AD, use`net localgroup administrators /add "AzureAD\UserUpn"`

## <a name="considerations"></a>Considerações 

Não é possível atribuir grupos à função de administrador do dispositivo, sendo permitido apenas utilizadores individuais.

Os administradores de dispositivos são atribuídos a todos os dispositivos Azure AD Joined. Não podem ser examinados para um conjunto específico de dispositivos.

Quando remove os utilizadores da função de administrador do dispositivo, eles ainda têm o privilégio do administrador local num dispositivo desde que sejam inscritos no mesmo. O privilégio é revogado durante o próximo início de sessão, ou após 4 horas quando um novo token primário é emitido.

## <a name="next-steps"></a>Passos seguintes

- Para obter uma descrição geral sobre como gerir dispositivos no portal do Azure, veja [Managing devices using the Azure portal](device-management-azure-portal.md) (Gerir dispositivos no portal do Azure)
- Para saber mais sobre o Acesso Condicional baseado no dispositivo, consulte as políticas de acesso condicional baseadas em [dispositivos Azure Ative Diretórios](../conditional-access/require-managed-devices.md).
