---
title: Operações do Azure Active Directory Connect Health
description: Este artigo descreve as operações adicionais que podem ser executadas depois de ter implementado o Azure AD Connect Health.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: daveba
ms.assetid: 86cc3840-60fb-43f9-8b2a-8598a9df5c94
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/18/2017
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 090a066afb24c4776f9844b8850264ffad842c59
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60350155"
---
# <a name="azure-active-directory-connect-health-operations"></a>Operações do Azure Active Directory Connect Health
Este tópico descreve as várias operações que pode realizar com o Azure Active Directory (Azure AD) Connect Health.

## <a name="enable-email-notifications"></a>Ativar notificações por e-mail
Pode configurar o serviço do Azure AD Connect Health para enviar notificações por e-mail quando alertas indicam que a sua infraestrutura de identidade não está em bom estada. Isto ocorre quando é gerado um alerta e, quando está resolvido.

![Definições de notificação de e-mail do captura de ecrã do Azure AD Connect Health](./media/how-to-connect-health-operations/email_noti_discover.png)

> [!NOTE]
> Notificações de e-mail estão ativadas por predefinição.
>
>

### <a name="to-enable-azure-ad-connect-health-email-notifications"></a>Para ativar notificações de e-mail do Azure AD Connect Health
1. Abra o **alertas** painel para o serviço para o qual pretende receber a notificação por e-mail.
2. A partir da barra de ação, clique em **definições de notificação**.
3. No comutador de notificação por e-mail, selecione **ON**.
4. Selecione a caixa de verificação se pretender que todos os administradores globais para receber notificações por e-mail.
5. Se pretender receber notificações de e-mail em outros endereços de e-mail, especifique-os na **destinatários de E-Mail adicionais** caixa. Para remover um endereço de e-mail desta lista, clique com o botão direito a entrada e selecione **eliminar**.
6. Para finalizar as alterações, clique em **guardar**. As alterações entrem em vigor apenas depois de guardar.

## <a name="delete-a-server-or-service-instance"></a>Eliminar uma instância de servidor ou serviço

>[!NOTE] 
> Licença de premium do Azure AD é necessária para obter os passos de eliminação.

Em alguns casos, poderá querer remover um servidor a ser monitorizado. Eis o que precisa saber para remover um servidor do serviço do Azure AD Connect Health.

Quando estiver a eliminar um servidor, tenha em atenção o seguinte:

* Esta ação para recolher mais dados a partir desse servidor. Este servidor é removido do serviço de monitoramento. Depois desta ação, não é possível visualizar novos alertas, monitorização ou dados de análise de utilização para este servidor.
* Esta ação não desinstala o agente de estado de funcionamento do seu servidor. Se não o desinstalou o agente de estado de funcionamento antes de executar este passo, poderá ver erros relacionados com o agente de estado de funcionamento no servidor.
* Esta ação não elimina os dados recolhidos a partir deste servidor. Os dados serem eliminados de acordo com a política de retenção de dados do Azure.
* Depois de efetuar esta ação, se deseja iniciar a monitorização do mesmo servidor novamente, tem de desinstalar e reinstalar o agente de estado de funcionamento neste servidor.

### <a name="delete-a-server-from-the-azure-ad-connect-health-service"></a>Eliminar um servidor do serviço do Azure AD Connect Health

>[!NOTE] 
> Licença de premium do Azure AD é necessária para obter os passos de eliminação.

O Azure AD Connect Health para serviços de Federação do Active Directory (AD FS) e do Azure AD Connect (sincronização):

1. Abra o **servidor** painel da **lista de servidores** painel ao selecionar o nome do servidor a ser removido.
2. Sobre o **servidor** painel, a partir da barra de ação, clique em **eliminar**.
![Captura de ecrã do Azure AD Connect Health eliminar servidor](./media/how-to-connect-health-operations/DeleteServer2.png)
3. Certifique-se ao escrever o nome do servidor na caixa de confirmação.
4. Clique em **Eliminar**.

Azure AD Connect Health para o Azure Active Directory Domain Services:

1. Abra o **controladores de domínio** dashboard.
2. Selecione o controlador de domínio a ser removido.
3. A partir da barra de ação, clique em **eliminar selecionado**.
4. Confirme a ação para eliminar o servidor.
5. Clique em **Eliminar**.

### <a name="delete-a-service-instance-from-azure-ad-connect-health-service"></a>Eliminar uma instância de serviço do serviço do Azure AD Connect Health
Em alguns casos, poderá querer remover uma instância de serviço. Eis o que precisa saber para remover uma instância de serviço do serviço do Azure AD Connect Health.

Quando estiver a eliminar uma instância de serviço, tenha em atenção o seguinte:

* Esta ação remove a instância de serviço atual do serviço de monitoramento.
* Esta ação não desinstalar ou remover o agente de estado de funcionamento de qualquer um dos servidores que foram monitorizados como parte desta instância de serviço. Se não o desinstalou o agente de estado de funcionamento antes de executar este passo, poderá ver erros relacionados com o agente de estado de funcionamento nos servidores.
* Todos os dados desta instância de serviço são eliminados de acordo com a política de retenção de dados do Azure.
* Depois de efetuar esta ação, se quiser iniciar a monitorização do serviço, desinstale e reinstale o agente de estado de funcionamento em todos os servidores. Depois de efetuar esta ação, se quiser iniciar a monitorização novamente o mesmo servidor, desinstalar, reinstalar e registar o agente de estado de funcionamento nesse servidor.

#### <a name="to-delete-a-service-instance-from-the-azure-ad-connect-health-service"></a>Para eliminar uma instância de serviço do serviço do Azure AD Connect Health
1. Abra o **serviço** painel da **lista de serviço** painel selecionando o identificador de serviço (nome de farm) que pretende remover. 
2. Sobre o **serviço** painel, a partir da barra de ação, clique em **eliminar**. 
![Captura de ecrã do Azure AD Connect Health Eliminar serviço](./media/how-to-connect-health-operations/DeleteServer.png)
3. Confirme ao escrever o nome do serviço na caixa de confirmação (por exemplo: sts.contoso.com).
4. Clique em **Eliminar**.
   <br><br>

[//]: # (Início da seção RBAC)
## <a name="manage-access-with-role-based-access-control"></a>Gerir o acesso com controlo de acesso baseado em funções
[Controlo de acesso baseado em funções (RBAC)](../../role-based-access-control/role-assignments-portal.md) para o Azure AD Connect Health fornece acesso a utilizadores e grupos que não sejam administradores globais. RBAC atribui funções para os usuários e grupos e fornece um mecanismo para limitar os administradores globais no seu diretório.

### <a name="roles"></a>Funções
O Azure AD Connect Health suporta as seguintes funções internas:

| Função | Permissões |
| --- | --- |
| Owner |Os proprietários podem *gerir o acesso* (por exemplo, atribuir uma função a um utilizador ou grupo), *ver todas as informações* (por exemplo, ver alertas) no portal, e *alterar as definições de* (para exemplo, notificações por e-mail) no Azure AD Connect Health. <br>Por predefinição, os administradores globais do Azure AD são atribuídos esta função e não pode ser alterado. |
| Contribuinte |Os contribuintes podem *ver todas as informações* (por exemplo, ver alertas) no portal, e *alterar as definições de* (por exemplo, notificações por e-mail) no Azure AD Connect Health. |
| Leitor |Os leitores podem *ver todas as informações* (por exemplo, ver alertas) do portal no Azure AD Connect Health. |

Todas as outras funções (por exemplo, os administradores de acesso de utilizador ou utilizadores do DevTest Labs) não tem nenhum impacto sobre a aceder no Azure AD Connect Health, mesmo que as funções estão disponíveis na experiência do portal.

### <a name="access-scope"></a>Âmbito de acesso
O Azure AD Connect Health oferece suporte a gerir o acesso em dois níveis:

* **Todas as instâncias de serviço**: Este é o caminho recomendado na maioria dos casos. Ele controla o acesso a todas as instâncias de serviço (por exemplo, um farm do AD FS) em todos os tipos de função que estão a ser monitorizados pelo Azure AD Connect Health.
* **Instância de serviço**: Em alguns casos, poderá ter de segregar o acesso com base em tipos de função ou por uma instância de serviço. Neste caso, pode gerir o acesso ao nível da instância de serviço.  

Permissão é concedida se um utilizador final tem acesso no diretório ou o serviço de nível de instância.

### <a name="allow-users-or-groups-access-to-azure-ad-connect-health"></a>Permitir que utilizadores ou o acesso de grupos para o Azure AD Connect Health
Os passos seguintes mostram como permitir o acesso.
#### <a name="step-1-select-the-appropriate-access-scope"></a>Passo 1: Selecione o âmbito de acesso apropriado
Para permitir um acesso de utilizador com o *todas as instâncias de serviço* nível no Azure AD Connect Health, abra o painel principal no Azure AD Connect Health.<br>

#### <a name="step-2-add-users-and-groups-and-assign-roles"></a>Passo 2: Adicionar utilizadores e grupos e atribuir funções
1. Partir do **configurar** secção, clique em **utilizadores**.<br>
   ![Barra lateral de recursos do captura de ecrã do Azure AD Connect Health](./media/how-to-connect-health-operations/startRBAC.png)
2. Selecione **Adicionar**.
3. Na **selecionar uma função** painel, selecione uma função (por exemplo, **proprietário**).<br>
   ![Janela de ligar utilizadores de RBAC do Estado de funcionamento de captura de ecrã do Azure AD](./media/how-to-connect-health-operations/RBAC_add.png)
4. Escreva o nome ou o identificador do destino de utilizador ou grupo. Pode selecionar um ou mais utilizadores ou grupos ao mesmo tempo. Clique em **Selecionar**.
   ![Janela de ligar utilizadores de RBAC do Estado de funcionamento de captura de ecrã do Azure AD](./media/how-to-connect-health-operations/RBAC_select_users.png)
5. Selecione **OK**.<br>
6. Depois de concluída a atribuição de função, os utilizadores e grupos são apresentadas na lista.<br>
   ![Janela de ligar utilizadores de RBAC do Estado de funcionamento de captura de ecrã do Azure AD, com os novos utilizadores realçada](./media/how-to-connect-health-operations/RBAC_user_list.png)

Os usuários listados e grupos têm agora acesso, de acordo com suas funções.

> [!NOTE]
> * Os administradores globais têm sempre acesso completo a todas as operações, mas as contas de administrador global não estão presentes na lista anterior.
> * A funcionalidade de convidar utilizadores não é suportada no Azure AD Connect Health.
>
>

#### <a name="step-3-share-the-blade-location-with-users-or-groups"></a>Passo 3: Partilhar a localização do painel com utilizadores ou grupos
1. Depois de atribuir permissões, um usuário pode acessar o Azure AD Connect Health indo [aqui](https://aka.ms/aadconnecthealth).
2. No painel, o utilizador pode afixar o painel ou partes diferentes do mesmo, ao dashboard. Basta clicar o **afixar ao dashboard** ícone.<br>
   ![Captura de ecrã do Azure AD Connect RBAC de estado de funcionamento afixar painel, com o ícone de afixação realçado](./media/how-to-connect-health-operations/RBAC_pin_blade.png)

> [!NOTE]
> Um utilizador com a função de leitor atribuída não é possível obter a extensão do Azure AD Connect Health no Azure Marketplace. O utilizador não é possível efetuar as informações necessárias "Criar" operação para fazer isso. O utilizador ainda pode receber para o painel acedendo a ligação anterior. Para uso subseqüente, o utilizador pode afixar painel ao dashboard.
>
>

### <a name="remove-users-or-groups"></a>Remover utilizadores ou grupos
Pode remover um utilizador ou grupo adicionado do Azure AD Connect Health RBAC. Simplesmente com o botão direito do utilizador ou grupo e selecione **remover**.<br>
![Janela de ligar utilizadores de RBAC do Estado de funcionamento de captura de ecrã do Azure AD, com remover realçada](./media/how-to-connect-health-operations/RBAC_remove.png)

[//]: # (Fim da secção RBAC)

## <a name="next-steps"></a>Passos Seguintes
* [Azure AD Connect Health](whatis-hybrid-identity-health.md)
* [Instalação do Agente do Azure AD Connect Health](how-to-connect-health-agent-install.md)
* [Utilizar o Azure AD Connect Health com o AD FS](how-to-connect-health-adfs.md)
* [Utilizar o Azure AD Connect Health para sincronização](how-to-connect-health-sync.md)
* [Utilizar o Azure AD Connect Health com o AD DS](how-to-connect-health-adds.md)
* [FAQ do Azure AD Connect Health](reference-connect-health-faq.md)
* [Histórico de versões do Azure AD Connect Health](reference-connect-health-version-history.md)
