---
title: 'Azure AD Connect: Passos seguintes e como gerir o Azure AD Connect | Documentos da Microsoft'
description: Saiba como estender a configuração predefinida e tarefas operacionais do Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: c18bee36-aebf-4281-b8fc-3fe14116f1a5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 291b3d506993cfea89be072684835c0d4efe75f6
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58095454"
---
# <a name="next-steps-and-how-to-manage-azure-ad-connect"></a>Passos seguintes e como gerir o Azure AD Connect
Utilize os procedimentos operacionais neste artigo para personalizar o Azure Active Directory (Azure AD) Connect para atender às necessidades e requisitos da sua organização.  

## <a name="add-additional-sync-admins"></a>Adicionar administradores de sincronização adicionais
Por predefinição, apenas o utilizador que fez a instalação e os administradores locais são capazes de gerir o motor de sincronização instalado. Para outras pessoas possam aceder e gerir o motor de sincronização, localize o grupo com o nome ADSyncAdmins no servidor local e adicioná-las a este grupo.

## <a name="assign-licenses-to-azure-ad-premium-and-enterprise-mobility-suite-users"></a>Atribuir licenças aos utilizadores do Azure AD Premium e o Enterprise Mobility Suite
Agora que os utilizadores foram sincronizados para a cloud, terá de atribuir-lhes uma licença para que eles podem comece a trabalhar com aplicações na cloud como o Office 365.

### <a name="to-assign-an-azure-ad-premium-or-enterprise-mobility-suite-license"></a>Para atribuir um Azure AD Premium ou uma licença do Enterprise Mobility Suite

1. Inicie sessão no portal do Azure como um administrador.
2. No lado esquerdo, selecione **Active Directory**.
3. Sobre o **do Active Directory** página, faça duplo clique no diretório que tem os utilizadores que pretende configurar.
4. Na parte superior da página do diretório, selecione **Licenças**.
5. Sobre o **licenças** página, selecione **Active Directory Premium** ou **Enterprise Mobility Suite**e, em seguida, clique em **atribuir**.
6. Na caixa de diálogo, selecione os utilizadores a quem pretende atribuir as licenças e clique no ícone de marca de verificação para guardar as alterações.

## <a name="verify-the-scheduled-synchronization-task"></a>Certifique-se a tarefa de sincronização agendada
Utilize o portal do Azure para verificar o estado de uma sincronização.

### <a name="to-verify-the-scheduled-synchronization-task"></a>Para verificar a tarefa de sincronização agendada
1. Inicie sessão no portal do Azure como um administrador.
2. No lado esquerdo, selecione **Active Directory**.
3. Sobre o **do Active Directory** página, faça duplo clique no diretório que tem os utilizadores que pretende configurar.
4. Na parte superior da página do diretório, selecione **integração de diretórios**.
5. Sob **integração com o local active directory**, tenha em atenção a hora da última sincronização.

<center>

![Hora de sincronização de diretório](./media/how-to-connect-post-installation/verify.png)</center>

## <a name="start-a-scheduled-synchronization-task"></a>Iniciar uma tarefa de sincronização agendada
Se precisar de executar uma tarefa de sincronização, pode fazê-lo ao executar novamente o Assistente do Azure AD Connect.  Tem de fornecer as credenciais do Azure AD.  No assistente, selecione o **personalizar as opções de sincronização** de tarefas e clique em **próxima** para percorrer o assistente. No final, certifique-se de que o **iniciar o processo de sincronização assim que a configuração inicial for concluída** caixa está selecionada.

<center>

![Iniciar a sincronização](./media/how-to-connect-post-installation/startsynch.png)</center>

Para obter mais informações sobre a agendador de sincronização do Azure AD Connect, consulte [Azure AD Connect Scheduler](how-to-connect-sync-feature-scheduler.md).

## <a name="additional-tasks-available-in-azure-ad-connect"></a>Tarefas adicionais disponíveis no Azure AD Connect
Após a instalação inicial do Azure AD Connect, pode sempre iniciar o assistente novamente do atalho de desktop ou de página de início do Azure AD Connect.  Observará que percorrer o assistente novamente fornece algumas novas opções sob a forma de tarefas adicionais.  

A tabela seguinte fornece um resumo dessas tarefas e uma breve descrição de cada tarefa.

![Lista de tarefas adicionais](./media/how-to-connect-post-installation/addtasks.png)

| Tarefas adicionais | Descrição |
| --- | --- |
| **Ver o cenário selecionado** |Ver a sua solução atual do Azure AD Connect.  Isso inclui configurações gerais, sincronizadas diretórios e definições de sincronização. |
| **Personalizar as opções de sincronização** |Altere a configuração atual, como adicionar florestas do Active Directory adicionais para a configuração ou a habilitação de opções de sincronização, tais como o utilizador, grupo, dispositivo ou repetição de escrita de palavras-passe. |
| **Ativar o modo de teste** |Informações de fase que não estão sincronizadas imediatamente e não são exportadas para o Azure AD ou no local do Active Directory.  Com esta funcionalidade, pode visualizar as sincronizações antes que ocorram. |

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre [integrar as identidades no local com o Azure Active Directory](whatis-hybrid-identity.md).
