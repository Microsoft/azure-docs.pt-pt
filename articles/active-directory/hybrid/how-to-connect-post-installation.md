---
title: 'Azure AD Connect: Próximos passos e como gerir o Azure AD Connect [ Ligação AD] Microsoft Docs'
description: Saiba como alargar a configuração predefinida e as tarefas operacionais para o Azure AD Connect.
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
ms.date: 04/26/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c204029557a73dc3f02015afb92c0fdbf0d4d50e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261297"
---
# <a name="next-steps-and-how-to-manage-azure-ad-connect"></a>Próximos passos e como gerir o Azure AD Connect
Utilize os procedimentos operacionais deste artigo para personalizar o Azure Ative Directory (Azure AD) Connect para atender às necessidades e requisitos da sua organização.  

## <a name="add-additional-sync-admins"></a>Adicionar administrações de sincronização adicionais
Por predefinição, apenas o utilizador que fez a instalação e os administradores locais são capazes de gerir o motor de sincronização instalado. Para que pessoas adicionais possam aceder e gerir o motor de sincronização, localize o grupo chamado ADSyncAdmins no servidor local e adicione-os a este grupo.

## <a name="assign-licenses-to-azure-ad-premium-and-enterprise-mobility-suite-users"></a>Atribuir licenças aos utilizadores do Azure AD Premium e da Enterprise Mobility Suite
Agora que os seus utilizadores foram sincronizados com a nuvem, é necessário atribuir-lhes uma licença para que possam avançar com aplicações na nuvem como o Office 365.

### <a name="to-assign-an-azure-ad-premium-or-enterprise-mobility-suite-license"></a>Para atribuir uma Licença azure AD Premium ou Enterprise Mobility Suite

1. Inscreva-se no portal Azure como administrador.
2. À esquerda, selecione **Ative Directy**.
3. Na página **do Diretório Ativo,** clique duas vezes no diretório que tem os utilizadores que pretende configurar.
4. Na parte superior da página do diretório, selecione **Licenças**.
5. Na página **licenças,** selecione **Ative Directory Premium** ou **Enterprise Mobility Suite,** e, em seguida, clique em **Atribuir**.
6. Na caixa de diálogo, selecione os utilizadores a quem pretende atribuir as licenças e clique no ícone de marca de verificação para guardar as alterações.

## <a name="verify-the-scheduled-synchronization-task"></a>Verifique a tarefa de sincronização programada
Utilize o portal Azure para verificar o estado de uma sincronização.

### <a name="to-verify-the-scheduled-synchronization-task"></a>Para verificar a tarefa de sincronização programada
1. Inscreva-se no portal Azure como administrador.
2. À esquerda, selecione **Ative Directy**.
3. À esquerda, selecione **Azure AD Connect**
4. No topo da página, note a última sincronização.

![Tempo de sincronização de diretório](./media/how-to-connect-post-installation/verify2.png)

## <a name="start-a-scheduled-synchronization-task"></a>Iniciar uma tarefa de sincronização programada
Se precisar de executar uma tarefa de sincronização, pode fazê-lo:

1. Clique duas vezes no atalho de secretária Azure AD Connect para iniciar o assistente.
2. Clique em **Configurar**.
3. No ecrã de tarefas, selecione as opções de **sincronização Personalizar** e clique **em Seguinte**
4. Introduzir as credenciais do Azure AD
5. Clique em **Seguinte**. Clique em **Seguinte**.  Clique em **Seguinte**.
5.  No ecrã **Ready to Configure,** certifique-se de que o iniciar o processo de **sincronização quando a configuração completa** a caixa é selecionada.
6.  Clique em **Configurar**.

Para obter mais informações sobre o Programador de sincronização Azure AD Connect, consulte o Programador de [Ligação AD Azure](how-to-connect-sync-feature-scheduler.md).

## <a name="additional-tasks-available-in-azure-ad-connect"></a>Tarefas adicionais disponíveis no Azure AD Connect
Após a instalação inicial do Azure AD Connect, pode sempre voltar a ligar o assistente a partir da página de arranque do Azure AD Connect ou do atalho para desktop.  Você vai notar que passar pelo assistente novamente fornece algumas novas opções sob a forma de tarefas adicionais.  

A tabela seguinte apresenta um resumo destas tarefas e uma breve descrição de cada tarefa.

![Lista de tarefas adicionais](./media/how-to-connect-post-installation/addtasks2.png)

| Tarefa adicional | Descrição |
| --- | --- |
|**Definições de Privacidade**|Veja quais os dados de telemetria que estão a ser partilhados com a Microsoft.|
|**Ver configuração atual**|Consulte a sua atual solução Azure AD Connect.  Isto inclui configurações gerais, diretórios sincronizados e definições de sincronização. |
| **Personalizar opções de sincronização** |Altere a configuração atual como adicionar florestas de Diretório Ativo adicionais à configuração, ou permitir opções de sincronização como utilizador, grupo, dispositivo ou reutilização de palavra-passe. |
|**Configure as opções do dispositivo**|Opções do dispositivo disponíveis para sincronização|
|**Refresh diretório schema**|Permite-lhe adicionar novos objetos de diretório no local para sincronização|
|**Modo de preparação de configurar** |Informações de estágio que não são imediatamente sincronizadas e não são exportadas para azure AD ou no local Diretório Ativo.  Com esta funcionalidade, pode pré-visualizar as sincronizações antes de ocorrerem. |
|**Alterar o sessão de inscrição do utilizador**|Alterar o método de autenticação que os utilizadores estão a usar para iniciar sessão|
|**Gerir a federação**|Gerencie a sua infraestrutura AD FS, renove certificados e adicione servidores AD FS|
|**Resolução de problemas**|Ajuda na resolução de problemas problemas azure AD Connect|

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [a integração das suas identidades no local com o Diretório Ativo Azure.](whatis-hybrid-identity.md)
