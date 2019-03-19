---
title: Dispositivos associados ao como gerir o grupo de administradores locais no Azure AD | Documentos da Microsoft
description: Saiba como atribuir funções do Azure para o grupo de administradores locais de um dispositivo Windows.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/08/2019
ms.author: markvi
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4265f82048f41b032644994683a71f596b8db1d4
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58167121"
---
# <a name="how-to-manage-the-local-administrators-group-on-azure-ad-joined-devices"></a>Dispositivos associados ao como gerir o grupo de administradores locais no Azure AD

Para gerir um dispositivo de Windows, terá de ser um membro do grupo Administradores local. Como parte do processo de associação do Azure Active Directory (Azure AD), o Azure AD atualiza a associação deste grupo num dispositivo. Pode personalizar a atualização de associação para satisfazer os seus requisitos empresariais. Uma atualização de associação é, por exemplo, útil se pretender ativar a sua equipa de suporte técnico fazer tarefas que exigem direitos de administrador num dispositivo.

Este artigo explica como funciona a atualização de associação e como pode personalizá-lo durante uma associação ao Azure AD. O conteúdo deste artigo não se aplica a um **híbrida** associação do Azure AD.


## <a name="how-it-works"></a>Como funciona

Quando se liga um dispositivo Windows com o Azure AD com uma associação do Azure AD, o Azure AD adiciona os seguintes princípios de segurança para o grupo de administradores locais no dispositivo:

- A função de administrador global do Azure AD
- A função de administrador de dispositivo do Azure AD 
- O utilizador que executa a associação do Azure AD   

Ao adicionar funções do Azure AD para o grupo de administradores locais, pode atualizar os utilizadores que podem gerir um dispositivo em qualquer altura no Azure AD sem modificar nada no dispositivo. Atualmente, não é possível atribuir grupos a uma função de administrador.
O Azure AD também adiciona a função de administrador de dispositivo do Azure AD para o grupo de administradores locais para suportar o princípio do menor privilégio (PoLP). Além dos administradores globais, também pode ativar os utilizadores que tenham sido *apenas* atribuída a função de administrador do dispositivo para gerir um dispositivo. 


## <a name="manage-the-global-administrators-role"></a>Gerir a função de administradores globais

Para ver e atualizar a associação de função de administrador global, consulte:

- [Ver todos os membros de uma função de administrador no Azure Active Directory](../users-groups-roles/directory-manage-roles-portal.md)

- [Atribuir um utilizador a funções de administrador no Azure Active Directory](../fundamentals/active-directory-users-assign-role-azure-portal.md)


## <a name="manage-the-device-administrator-role"></a>Gerir a função de administrador do dispositivo 

No portal do Azure, pode gerir a função de administrador do dispositivo na **dispositivos** página. Para abrir o **dispositivos** página:

1. Inicie sessão no seu [portal do Azure](https://portal.azure.com) como um administrador global ou administrador do dispositivo.
2. Na barra de navegação esquerda, clique em **do Azure Active Directory**. 
3. Na **Manage** secção, clique em **dispositivos**.
4. Sobre o **dispositivos** página, clique em **definições do dispositivo**.

Para modificar a função de administrador do dispositivo, configure **administradores locais adicionais no Azure AD associado a um dispositivos**.  

![Administradores locais adicionais](./media/assign-local-admin/10.png)

>[!NOTE]
> Esta opção requer um inquilino do Azure AD Premium. 


Administradores de dispositivos são atribuídos a dispositivos de todos os Azure AD associado. Não é possível definir o âmbito administradores de dispositivos a um conjunto específico de dispositivos. A atualização da função de administrador do dispositivo necessariamente não tem um impacto imediato em utilizadores afetados. Para os dispositivos, já está conectado a um utilizador, a atualização de privilégios ocorre:
     

- Quando um utilizador inicia sessão.
- Depois de 4 horas, quando um novo principal atualizar Token é emitido. 




## <a name="manage-regular-users"></a>Gerir utilizadores regulares

Por predefinição, o Azure AD adiciona o utilizador que executa a associação do Azure AD ao grupo administrador no dispositivo. Se quiser evitar que usuários regulares de se tornar administradores locais, tem as seguintes opções:

- [Windows Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) -Windows Autopilot disponibiliza uma opção para impedir que o utilizador primário que executa a junção de se tornar um administrador local. Isso pode ser feito pela [criar um perfil de Autopilot](https://docs.microsoft.com/intune/enrollment-autopilot#create-an-autopilot-deployment-profile).
 
- [Inscrição em massa](https://docs.microsoft.com/intune/windows-bulk-enroll) -uma associação do Azure AD que é executada no contexto de uma inscrição em massa ocorre no contexto de um utilizador criada automaticamente. Os utilizadores a iniciar sessão depois de um dispositivo foi associado não são adicionados ao grupo de administradores.   



## <a name="manually-elevate-a-user-on-a-device"></a>Elevar manualmente um utilizador num dispositivo 

Além de usar o processo de associação do Azure AD, pode elevar também manualmente um utilizador normal para se tornar um administrador local num dispositivo específico. Este passo requer que já seja um membro do grupo Administradores local. 

Começando com o **Windows 10 1709** versão, pode realizar esta tarefa de **definições -> contas -> outros utilizadores**. Selecione **adicionar um utilizador profissional ou escolar**, introduza o UPN do utilizador em **conta de utilizador** e selecione *administrador* em **tipo de conta**  
 
Além disso, também pode adicionar utilizadores através da linha de comandos:

- Se os utilizadores de inquilino são sincronizados a partir do Active Directory no local, utilize `net localgroup administrators /add "Contoso\username"`.

- Se os utilizadores de inquilino são criados no Azure AD, utilize `net localgroup administrators /add "AzureAD\UserUpn"`


## <a name="considerations"></a>Considerações 

Não é possível atribuir grupos à função de administrador do dispositivo, são permitidos apenas os utilizadores individuais.

Administradores de dispositivos são atribuídos a todos os dispositivos do Azure AD associado. Eles não podem ser confinados para um conjunto específico de dispositivos.

Quando remover utilizadores da função de administrador do dispositivo, eles ainda têm o privilégio de administrador local num dispositivo, desde que iniciar a sessão para o mesmo. O privilégio é revogado durante o próximo início de sessão ou após 4 horas, quando é emitido um novo token de atualização principal.



## <a name="next-steps"></a>Passos Seguintes

- Para obter uma descrição geral sobre como gerir dispositivos no portal do Azure, veja [Managing devices using the Azure portal](device-management-azure-portal.md) (Gerir dispositivos no portal do Azure)

- Para saber mais sobre o acesso condicional baseado em dispositivos, veja [Configure Azure Active Directory device-based conditional access policies](../conditional-access/require-managed-devices.md) (Configurar políticas de acesso condicional baseadas no dispositivo do Azure Active Directory).


