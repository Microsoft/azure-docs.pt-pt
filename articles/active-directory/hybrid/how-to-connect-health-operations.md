---
title: Operações de Azure Active Directory Connect Health
description: Este artigo descreve as operações adicionais que podem ser executadas depois que você implantou o Azure AD Connect Health.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: daveba
ms.assetid: 86cc3840-60fb-43f9-8b2a-8598a9df5c94
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/18/2017
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef908429d359020282920d73480a472bfde0aa60
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76897153"
---
# <a name="azure-active-directory-connect-health-operations"></a>Operações de Azure Active Directory Connect Health
Este tópico descreve as várias operações que você pode executar usando o Azure Active Directory (Azure AD) Connect Health.

## <a name="enable-email-notifications"></a>Habilitar notificações por email
Você pode configurar o serviço de Azure AD Connect Health para enviar notificações por email quando alertas indicam que sua infraestrutura de identidade não está íntegra. Isso ocorre quando um alerta é gerado e quando ele é resolvido.

![Captura de tela de Azure AD Connect Health configurações de notificação por email](./media/how-to-connect-health-operations/email_noti_discover.png)

> [!NOTE]
> As notificações por email são habilitadas por padrão.
>

### <a name="to-enable-azure-ad-connect-health-email-notifications"></a>Para habilitar Azure AD Connect Health notificações por email
1. Abra a folha **alertas** para o serviço para o qual você deseja receber a notificação por email.
2. Na barra de ação, clique em **configurações de notificação**.
3. Na opção de notificação de email, selecione **ativado**.
4. Marque a caixa de seleção se desejar que todos os administradores globais recebam notificações por email.
5. Se você quiser receber notificações por email em qualquer outro endereço de email, especifique-as na caixa **destinatários de email adicionais** . Para remover um endereço de email dessa lista, clique com o botão direito do mouse na entrada e selecione **excluir**.
6. Para finalizar as alterações, clique em **salvar**. As alterações entram em vigor somente depois que você salvar.

>[!NOTE] 
> Quando há problemas ao processar solicitações de sincronização em nosso serviço de back-end, esse serviço envia um email de notificação com os detalhes do erro para os endereços de email de contato administrativo do seu locatário. Ouvimos comentários de clientes que, em determinados casos, o volume dessas mensagens é extremamente grande, portanto, estamos alterando a maneira como enviamos essas mensagens. 
>
> Em vez de enviar uma mensagem para cada erro de sincronização toda vez que ocorrer, enviaremos um resumo diário de todos os erros retornados pelo serviço de back-end. Isso permite que os clientes processem esses erros de maneira mais eficiente e reduzem o número de mensagens de erro duplicadas.
>
> Planejamos que essa alteração seja implementada em 15 de janeiro de 2020.

## <a name="delete-a-server-or-service-instance"></a>Excluir uma instância de serviço ou servidor

>[!NOTE] 
> A licença do Azure AD Premium é necessária para as etapas de exclusão.

Em alguns casos, talvez você queira remover um servidor do monitorado. Veja o que você precisa saber para remover um servidor do serviço de Azure AD Connect Health.

Quando você estiver excluindo um servidor, lembre-se do seguinte:

* Essa ação para de coletar dados adicionais desse servidor. Esse servidor é removido do serviço de monitoramento. Após essa ação, você não poderá exibir novos alertas, monitoramento ou dados de análise de uso para este servidor.
* Essa ação não desinstala o agente de integridade do servidor. Se você não tiver desinstalado o agente de integridade antes de executar esta etapa, você poderá ver erros relacionados ao agente de integridade no servidor.
* Essa ação não exclui os dados já coletados deste servidor. Esses dados são excluídos de acordo com a política de retenção de dados do Azure.
* Depois de executar essa ação, se você quiser iniciar o monitoramento do mesmo servidor novamente, será necessário desinstalar e reinstalar o agente de integridade nesse servidor.

### <a name="delete-a-server-from-the-azure-ad-connect-health-service"></a>Excluir um servidor do serviço de Azure AD Connect Health

>[!NOTE] 
> A licença do Azure AD Premium é necessária para as etapas de exclusão.

Azure AD Connect Health para Serviços de Federação do Active Directory (AD FS) (AD FS) e Azure AD Connect (sincronização):

1. Abra a folha do **servidor** na folha da **lista de servidores** selecionando o nome do servidor a ser removido.
2. Na folha do **servidor** , na barra de ação, clique em **excluir**.
![Screenshot do Azure AD Connect Health apagam](./media/how-to-connect-health-operations/DeleteServer2.png) do servidor
3. Confirme digitando o nome do servidor na caixa de confirmação.
4. Clique em **Eliminar**.

Azure AD Connect Health para Azure Active Directory Domain Services:

1. Abra o painel **controladores de domínio** .
2. Selecione o controlador de domínio a ser removido.
3. Na barra de ação, clique em **excluir selecionado**.
4. Confirme a ação para excluir o servidor.
5. Clique em **Eliminar**.

### <a name="delete-a-service-instance-from-azure-ad-connect-health-service"></a>Excluir uma instância de serviço do serviço Azure AD Connect Health
Em alguns casos, talvez você queira remover uma instância de serviço. Veja o que você precisa saber para remover uma instância de serviço do serviço Azure AD Connect Health.

Quando você estiver excluindo uma instância de serviço, lembre-se do seguinte:

* Essa ação remove a instância de serviço atual do serviço de monitoramento.
* Essa ação não desinstala ou remove o agente de integridade de nenhum dos servidores que foram monitorados como parte dessa instância de serviço. Se você não tiver desinstalado o agente de integridade antes de executar esta etapa, você poderá ver erros relacionados ao agente de integridade nos servidores.
* Todos os dados dessa instância de serviço são excluídos de acordo com a política de retenção de dados do Azure.
* Depois de executar essa ação, se você quiser iniciar o monitoramento do serviço, desinstale e reinstale o agente de integridade em todos os servidores. Depois de executar essa ação, se você quiser começar a monitorar o mesmo servidor novamente, desinstale, reinstale e registre o agente de integridade nesse servidor.

#### <a name="to-delete-a-service-instance-from-the-azure-ad-connect-health-service"></a>Para excluir uma instância de serviço do serviço de Azure AD Connect Health
1. Abra a folha de **serviço** na folha **lista de serviços** selecionando o identificador de serviço (nome do farm) que você deseja remover. 
2. Na folha **serviço** , na barra de ação, clique em **excluir**. 
![Screenshot da Azure AD Connect Health elimina mato de serviço](./media/how-to-connect-health-operations/DeleteServer.png)
3. Confirme digitando o nome do serviço na caixa de confirmação (por exemplo: sts.contoso.com).
4. Clique em **Eliminar**.
   <br><br>

[//]: # (Início da seção RBAC)
## <a name="manage-access-with-role-based-access-control"></a>Gerenciar o acesso com o controle de acesso baseado em função
O [RBAC (controle de acesso baseado em função)](../../role-based-access-control/role-assignments-portal.md) para Azure ad Connect Health fornece acesso a usuários e grupos diferentes de administradores globais. O RBAC atribui funções aos usuários e grupos pretendidos e fornece um mecanismo para limitar os administradores globais em seu diretório.

### <a name="roles"></a>Funções
O Azure AD Connect Health dá suporte às seguintes funções internas:

| Função | Permissões |
| --- | --- |
| Proprietário |Os proprietários podem *gerenciar o acesso* (por exemplo, atribuir uma função a um usuário ou grupo), *Exibir todas as informações* (por exemplo, exibir alertas) no portal e *alterar as configurações* (por exemplo, notificações por email) em Azure ad Connect Health. <br>Por padrão, os administradores globais do Azure AD recebem essa função e isso não pode ser alterado. |
| Contribuinte |Os colaboradores podem *Exibir todas as informações* (por exemplo, exibir alertas) no portal e *alterar as configurações* (por exemplo, notificações por email) em Azure ad Connect Health. |
| Leitor |Os leitores podem *Exibir todas as informações* (por exemplo, exibir alertas) do portal no Azure ad Connect Health. |

Todas as outras funções (como administradores de acesso do usuário ou usuários do DevTest Labs) não têm impacto no acesso em Azure AD Connect Health, mesmo que as funções estejam disponíveis na experiência do Portal.

### <a name="access-scope"></a>Âmbito de acesso
O Azure AD Connect Health dá suporte ao gerenciamento de acesso em dois níveis:

* **Todas as instâncias de serviço**: esse é o caminho recomendado na maioria dos casos. Ele controla o acesso para todas as instâncias de serviço (por exemplo, um farm de AD FS) em todos os tipos de função que estão sendo monitorados pelo Azure AD Connect Health.
* **Instância de serviço**: em alguns casos, talvez seja necessário separar o acesso com base em tipos de função ou por uma instância de serviço. Nesse caso, você pode gerenciar o acesso no nível da instância de serviço.  

A permissão será concedida se um usuário final tiver acesso no nível do diretório ou da instância de serviço.

### <a name="allow-users-or-groups-access-to-azure-ad-connect-health"></a>Permitir que usuários ou grupos acessem Azure AD Connect Health
As etapas a seguir mostram como permitir o acesso.
#### <a name="step-1-select-the-appropriate-access-scope"></a>Etapa 1: selecionar o escopo de acesso apropriado
Para permitir que um usuário acesse o nível de *todas as instâncias de serviço* no Azure ad Connect Health, abra a folha principal no Azure ad Connect Health.<br>

#### <a name="step-2-add-users-and-groups-and-assign-roles"></a>Etapa 2: adicionar usuários e grupos e atribuir funções
1. Na seção **Configurar** , clique em **usuários**.<br>
   ![Screenshot da barra lateral de recursos Azure AD Connect Health](./media/how-to-connect-health-operations/startRBAC.png)
2. Selecione **Adicionar**.
3. No painel **selecionar uma função** , selecione uma função (por exemplo, **proprietário**).<br>
   ![Screenshot do Azure AD Connect Health RBAC Utilizadores janela](./media/how-to-connect-health-operations/RBAC_add.png)
4. Digite o nome ou o identificador do usuário ou grupo de destino. Você pode selecionar um ou mais usuários ou grupos ao mesmo tempo. Clique em **Selecionar**.
   ![Screenshot do Azure AD Connect Health RBAC Utilizadores janela](./media/how-to-connect-health-operations/RBAC_select_users.png)
5. Selecione **OK**.<br>
6. Depois que a atribuição de função for concluída, os usuários e grupos aparecerão na lista.<br>
   ![Screenshot da janela de utilizadores do Azure AD Connect Health RBAC, com novos utilizadores em destaque](./media/how-to-connect-health-operations/RBAC_user_list.png)

Agora, os usuários e grupos listados têm acesso, de acordo com suas funções atribuídas.

> [!NOTE]
> * Os administradores globais sempre têm acesso completo a todas as operações, mas as contas de administrador global não estão presentes na lista anterior.
> * O recurso convidar usuários não tem suporte no Azure AD Connect Health.
>
>

#### <a name="step-3-share-the-blade-location-with-users-or-groups"></a>Etapa 3: compartilhar o local da folha com usuários ou grupos
1. Depois de atribuir permissões, um usuário pode acessar Azure AD Connect Health acessando [aqui](https://aka.ms/aadconnecthealth).
2. Na folha, o usuário pode fixar a folha ou partes diferentes dela no painel. Basta clicar no ícone **fixar no painel** .<br>
   ![Screenshot da lâmina de pino RBAC de ligação a saúde azure, com ícone de pino destacado](./media/how-to-connect-health-operations/RBAC_pin_blade.png)

> [!NOTE]
> Um usuário com a função de leitor atribuída não é capaz de obter Azure AD Connect Health extensão do Azure Marketplace. O usuário não pode executar a operação "criar" necessária para fazer isso. O usuário ainda pode chegar à folha acessando o link anterior. Para uso posterior, o usuário pode fixar a folha no painel.
>
>

### <a name="remove-users-or-groups"></a>Remover usuários ou grupos
Você pode remover um usuário ou grupo adicionado ao Azure AD Connect Health RBAC. Basta clicar com o botão direito do mouse no usuário ou grupo e selecionar **remover**.<br>
![Screenshot da janela de utilizadores do Azure AD Connect Health RBAC, com remover destacado](./media/how-to-connect-health-operations/RBAC_remove.png)

[//]: # (Fim da seção RBAC)

## <a name="next-steps"></a>Passos seguintes
* [Azure AD Connect Health](whatis-hybrid-identity-health.md)
* [Instalação do Agente do Azure AD Connect Health](how-to-connect-health-agent-install.md)
* [Utilizar o Azure AD Connect Health com o AD FS](how-to-connect-health-adfs.md)
* [Utilizar o Azure AD Connect Health para sincronização](how-to-connect-health-sync.md)
* [Utilizar o Azure AD Connect Health com o AD DS](how-to-connect-health-adds.md)
* [FAQ do Azure AD Connect Health](reference-connect-health-faq.md)
* [Histórico de versões do Azure AD Connect Health](reference-connect-health-version-history.md)
