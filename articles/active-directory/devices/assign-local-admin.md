---
title: Como gerenciar o grupo de administradores locais em dispositivos adicionados ao Azure AD | Microsoft Docs
description: Saiba como atribuir funções do Azure ao grupo de administradores locais de um dispositivo Windows.
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
ms.openlocfilehash: f50ace6d707db35dfd7cf9f14026d755093a038c
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802442"
---
# <a name="how-to-manage-the-local-administrators-group-on-azure-ad-joined-devices"></a>Como gerenciar o grupo de administradores locais em dispositivos ingressados no Azure AD

Para gerenciar um dispositivo Windows, você precisa ser um membro do grupo Administradores local. Como parte do processo de ingresso do Azure Active Directory (Azure AD), o Azure AD atualiza a associação desse grupo em um dispositivo. Você pode personalizar a atualização de associação para atender às suas necessidades de negócios. Uma atualização de associação é, por exemplo, útil se você quiser permitir que sua equipe de suporte técnico realize tarefas que exijam direitos de administrador em um dispositivo.

Este artigo explica como a atualização de associação funciona e como você pode personalizá-la durante uma junção do Azure AD. O conteúdo deste artigo não se aplica a uma junção **híbrida** do Azure AD.

## <a name="how-it-works"></a>Como funciona

Quando você conecta um dispositivo Windows com o Azure AD usando uma junção do Azure AD, o Azure AD adiciona os seguintes princípios de segurança ao grupo local de administradores no dispositivo:

- A função de administrador global do Azure AD
- A função de administrador de dispositivos do Azure AD 
- O usuário que está executando a junção do Azure AD   

Ao adicionar funções do Azure AD ao grupo local de administradores, você pode atualizar os usuários que podem gerenciar um dispositivo a qualquer momento no Azure AD sem modificar nada no dispositivo. No momento, não é possível atribuir grupos a uma função de administrador.
O AD do Azure também adiciona a função de administrador de dispositivos do Azure AD ao grupo local de administradores para dar suporte ao princípio de privilégios mínimos (PoLP). Além dos administradores globais, você também pode habilitar os usuários que *só* receberam a função de administrador de dispositivos para gerenciar um dispositivo. 

## <a name="manage-the-global-administrators-role"></a>Gerenciar a função de administradores globais

Para exibir e atualizar a associação da função de administrador global, consulte:

- [Exibir todos os membros de uma função de administrador no Azure Active Directory](../users-groups-roles/directory-manage-roles-portal.md)
- [Atribuir um usuário a funções de administrador no Azure Active Directory](../fundamentals/active-directory-users-assign-role-azure-portal.md)


## <a name="manage-the-device-administrator-role"></a>Gerenciar a função de administrador do dispositivo 

No portal do Azure, você pode gerenciar a função de administrador do dispositivo na página **dispositivos** . Para abrir a página **dispositivos** :

1. Entre em seu [portal do Azure](https://portal.azure.com) como administrador global ou administrador de dispositivo.
1. Na barra de navegação à esquerda, clique em **Azure Active Directory**. 
1. Na seção **gerenciar** , clique em **dispositivos**.
1. Na página **dispositivos** , clique em **configurações do dispositivo**.

Para modificar a função de administrador do dispositivo, configure **Administradores locais adicionais em dispositivos ingressados no Azure ad**.  

![Administradores locais adicionais](./media/assign-local-admin/10.png)

>[!NOTE]
> Esta opção requer um locatário Azure AD Premium. 

Os administradores de dispositivo são atribuídos a todos os dispositivos ingressados no Azure AD. Você não pode definir o escopo de administradores de dispositivo para um conjunto específico de dispositivos. A atualização da função de administrador do dispositivo não tem necessariamente um impacto imediato nos usuários afetados. Para os dispositivos, um usuário já está conectado, a atualização de privilégio ocorre:

- Quando um usuário se desconecta.
- Após 4 horas, quando um novo token de atualização primário for emitido. 

## <a name="manage-regular-users"></a>Gerenciar usuários regulares

Por padrão, o Azure AD adiciona o usuário que está executando a junção do Azure AD ao grupo de administradores no dispositivo. Se você quiser impedir que usuários regulares se tornem administradores locais, terá as seguintes opções:

- [Windows AutoPilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) -o Windows AutoPilot fornece uma opção para impedir que o usuário primário execute o join de se tornar um administrador local. Você pode fazer isso [criando um perfil do AutoPilot](https://docs.microsoft.com/intune/enrollment-autopilot#create-an-autopilot-deployment-profile).
- [Registro em massa](https://docs.microsoft.com/intune/windows-bulk-enroll) – uma junção do Azure ad executada no contexto de um registro em massa ocorre no contexto de um usuário criado automaticamente. Os usuários que se conectarem depois que um dispositivo tiver sido ingressado não serão adicionados ao grupo de administradores.   

## <a name="manually-elevate-a-user-on-a-device"></a>Elevar um usuário manualmente em um dispositivo 

Além de usar o processo de ingresso no Azure AD, você também pode elevar manualmente um usuário normal para se tornar um administrador local em um dispositivo específico. Esta etapa exige que você já seja um membro do grupo local de administradores. 

A partir da versão **10 1709 do Windows** , você pode executar essa tarefa em **Configurações-> contas-> outros usuários**. Selecione **Adicionar um usuário corporativo ou de estudante**, insira o UPN do usuário **em conta de usuário** e selecione *administrador* em **tipo de conta**  
 
Além disso, você também pode adicionar usuários usando o prompt de comando:

- Se os usuários do locatário estiverem sincronizados do Active Directory local, use `net localgroup administrators /add "Contoso\username"`.
- Se os usuários do locatário forem criados no Azure AD, use `net localgroup administrators /add "AzureAD\UserUpn"`

## <a name="considerations"></a>Considerações 

Você não pode atribuir grupos à função de administrador do dispositivo, somente usuários individuais são permitidos.

Os administradores de dispositivo são atribuídos a todos os dispositivos ingressados no Azure AD. Eles não podem ter o escopo definido para um conjunto específico de dispositivos.

Quando você remove usuários da função de administrador do dispositivo, eles ainda têm o privilégio de administrador local em um dispositivo, desde que eles estejam conectados a ele. O privilégio é revogado durante a próxima entrada ou após 4 horas quando um novo token de atualização primário é emitido.

## <a name="next-steps"></a>Passos seguintes

- Para obter uma descrição geral sobre como gerir dispositivos no portal do Azure, veja [Managing devices using the Azure portal](device-management-azure-portal.md) (Gerir dispositivos no portal do Azure)
- Para saber mais sobre o acesso condicional baseado em dispositivo, confira [configurar Azure Active Directory políticas de acesso condicional com base no dispositivo](../conditional-access/require-managed-devices.md).
