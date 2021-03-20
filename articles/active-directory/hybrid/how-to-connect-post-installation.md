---
title: 'Azure AD Connect: Próximos passos e como gerir o Azure AD Connect | Microsoft Docs'
description: Saiba como estender a configuração predefinida e as tarefas operacionais para Azure AD Connect.
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
ms.topic: how-to
ms.date: 04/26/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7d6689b4b67b8462e983ae9b111e0fbc60c422b7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "89657112"
---
# <a name="next-steps-and-how-to-manage-azure-ad-connect"></a>Próximos passos e como gerir a Azure AD Connect
Utilize os procedimentos operacionais deste artigo para personalizar o Azure Ative Directory (Azure AD) Connect para atender às necessidades e requisitos da sua organização.  

## <a name="add-additional-sync-admins"></a>Adicione administradores de sincronização adicionais
Por predefinição, apenas o utilizador que fez a instalação e os administradores locais são capazes de gerir o motor de sincronização instalado. Para que pessoas adicionais possam aceder e gerir o motor de sincronização, localize o grupo chamado ADSyncAdmins no servidor local e adicione-os a este grupo.

## <a name="assign-licenses-to-azure-ad-premium-and-enterprise-mobility-suite-users"></a>Atribuir licenças aos utilizadores do Azure AD Premium e da Enterprise Mobility Suite
Agora que os seus utilizadores foram sincronizados na nuvem, precisa de lhes atribuir uma licença para que possam avançar com aplicações na nuvem como a Microsoft 365.

### <a name="to-assign-an-azure-ad-premium-or-enterprise-mobility-suite-license"></a>Para atribuir uma Licença Azure AD Premium ou Suíte de Mobilidade Empresarial

1. Inscreva-se no portal Azure como administrador.
2. À esquerda, selecione **Ative Directory**.
3. Na página **Ative Directory,** clique duas vezes no diretório que tem os utilizadores que pretende configurar.
4. Na parte superior da página do diretório, selecione **Licenças**.
5. Na página **Licenças,** selecione **Ative Directory Premium** ou **Enterprise Mobility Suite**, e, em seguida, clique em **Atribuir**.
6. Na caixa de diálogo, selecione os utilizadores a quem pretende atribuir as licenças e clique no ícone de marca de verificação para guardar as alterações.

## <a name="verify-the-scheduled-synchronization-task"></a>Verifique a tarefa de sincronização programada
Utilize o portal Azure para verificar o estado de sincronização.

### <a name="to-verify-the-scheduled-synchronization-task"></a>Para verificar a tarefa de sincronização programada
1. Inscreva-se no portal Azure como administrador.
2. À esquerda, selecione **Ative Directory**.
3. À esquerda, selecione **Azure AD Connect**
4. No topo da página, note a última sincronização.

![Tempo de sincronização do diretório](./media/how-to-connect-post-installation/verify2.png)

## <a name="start-a-scheduled-synchronization-task"></a>Iniciar uma tarefa de sincronização programada
Se precisar de executar uma tarefa de sincronização, pode fazê-lo através de:

1. Clique duas vezes no atalho de ambiente de trabalho Azure AD Connect para iniciar o assistente.
2. Clique em **Configurar**.
3. No ecrã de tarefas, selecione as **opções de sincronização de Personalização** e clique em **Seguinte**
4. Introduzir as credenciais do Azure AD
5. Clique em **Seguinte**. Clique em **Seguinte**.  Clique em **Seguinte**.
5.  No ecrã **Pronto para Configurar,** certifique-se de que o **processo de sincronização iniciar quando a configuração completa** a caixa é selecionado.
6.  Clique em **Configurar**.

Para obter mais informações sobre o Azure AD Connect Sync Scheduler, consulte [o Azure AD Connect Scheduler](how-to-connect-sync-feature-scheduler.md).

## <a name="additional-tasks-available-in-azure-ad-connect"></a>Tarefas adicionais disponíveis no Azure AD Connect
Após a instalação inicial do Azure AD Connect, pode sempre recomeçar o assistente a partir da página inicial do Azure AD Connect ou do atalho do ambiente de trabalho.  Irá notar que passar pelo assistente novamente fornece algumas novas opções sob a forma de tarefas adicionais.  

A tabela seguinte fornece um resumo destas tarefas e uma breve descrição de cada tarefa.

![Lista de tarefas adicionais](./media/how-to-connect-post-installation/addtasks2.png)

| Tarefa adicional | Description |
| --- | --- |
|**Definições de Privacidade**|Veja quais os dados de telemetria que estão a ser partilhados com a Microsoft.|
|**Ver configuração atual**|Veja a sua solução atual Azure AD Connect.  Isto inclui configurações gerais, diretórios sincronizados e configurações de sincronização. |
| **Personalize opções de sincronização** |Altere a configuração atual como adicionar florestas de Ative Directory adicionais à configuração ou permitir opções de sincronização como utilizador, grupo, dispositivo ou desativação de palavras-passe. |
|**Opções de configuração do dispositivo**|Opções do dispositivo disponíveis para sincronização|
|**Refresh directy schema**|Permite-lhe adicionar novos objetos de diretório no local para sincronização|
|**Modo de Configuração** |Informações de fase que não são imediatamente sincronizadas e não são exportadas para Azure AD ou no local Ative Directory.  Com esta funcionalidade, pode pré-visualizar as sincronizações antes de ocorrerem. |
|**Alterar o pedido de in do utilizador**|Alterar o método de autenticação que os utilizadores estão a usar para iniciar sação|
|**Gerir federação**|Gerencie a sua infraestrutura AD FS, renove certificados e adicione servidores AD FS|
|**Resolução de problemas**|Ajude na resolução de problemas problemas Azure AD Connect|

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [a integração das suas identidades no local com o Azure Ative Directory](whatis-hybrid-identity.md).
