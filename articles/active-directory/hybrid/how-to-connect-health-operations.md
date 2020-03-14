---
title: Operações de Ligação ativa do Diretório Azure
description: Este artigo descreve operações adicionais que podem ser realizadas depois de ter implantado a Azure AD Connect Health.
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79261518"
---
# <a name="azure-active-directory-connect-health-operations"></a>Operações de Ligação ativa do Diretório Azure
Este tópico descreve as várias operações que pode realizar utilizando o Azure Ative Directory (Azure AD) Connect Health.

## <a name="enable-email-notifications"></a>Ativar notificações de e-mail
Pode configurar o serviço Azure AD Connect Health para enviar notificações por e-mail quando os alertas indicam que a sua infraestrutura de identidade não é saudável. Isto ocorre quando um alerta é gerado, e quando é resolvido.

![Screenshot das definições de notificação de email azure AD Connect Health](./media/how-to-connect-health-operations/email_noti_discover.png)

> [!NOTE]
> As notificações por e-mail são ativadas por defeito.
>

### <a name="to-enable-azure-ad-connect-health-email-notifications"></a>Para ativar as notificações de email da Azure AD Connect Health
1. Abra a lâmina **alertas** para o serviço para o qual pretende receber notificação por e-mail.
2. A partir da barra de ação, clique em **Definições de Notificação**.
3. No interruptor de notificação de e-mail, selecione **ON**.
4. Selecione a caixa de verificação se quiser que todos os administradores globais recebam notificações de e-mail.
5. Se pretender receber notificações de e-mail em quaisquer outros endereços de e-mail, especifique-as na caixa de Destinatários de **Email Adicional.** Para remover um endereço de e-mail desta lista, clique na entrada e selecione **Eliminar**.
6. Para finalizar as alterações, clique em **Guardar**. As alterações só têm efeito depois de poupar.

>[!NOTE] 
> Quando existem problemas de processamento de pedidos de sincronização no nosso serviço backend, este serviço envia um e-mail de notificação com os detalhes do erro para o endereço de e-mail de contacto administrativo(es) do seu inquilino. Ouvimos comentários de clientes que, em certos casos, o volume destas mensagens é proibitivamente grande, pelo que estamos a mudar a forma como enviamos estas mensagens. 
>
> Em vez de enviar uma mensagem para cada erro de sincronização cada vez que ocorrer, enviaremos uma digestão diária de todos os erros que o serviço backend tenha devolvido. Isto permite que os clientes processem estes erros de uma forma mais eficiente e reduz o número de mensagens de erro duplicadas.
>
> Planeamos que esta mudança seja implementada a 15 de janeiro de 2020.

## <a name="delete-a-server-or-service-instance"></a>Eliminar um servidor ou instância de serviço

>[!NOTE] 
> A licença premium Azure AD é necessária para as etapas de eliminação.

Em alguns casos, é melhor remover um servidor de ser monitorizado. Aqui está o que precisa de saber para remover um servidor do serviço Azure AD Connect Health.

Quando estiver a apagar um servidor, esteja atento ao seguinte:

* Esta ação deixa de recolher mais dados desse servidor. Este servidor é removido do serviço de monitorização. Após esta ação, não é possível visualizar novos alertas, monitorização ou dados de análise de utilização para este servidor.
* Esta ação não desinstala o Agente de Saúde do seu servidor. Se não tiver desinstalado o Agente de Saúde antes de realizar este passo, poderá ver erros relacionados com o Agente de Saúde no servidor.
* Esta ação não elimina os dados já recolhidos deste servidor. Estes dados são eliminados de acordo com a política de retenção de dados do Azure.
* Depois de realizar esta ação, se pretender voltar a monitorizar o mesmo servidor, deve desinstalar e reinstalar o Agente de Saúde neste servidor.

### <a name="delete-a-server-from-the-azure-ad-connect-health-service"></a>Eliminar um servidor do serviço Azure AD Connect Health

>[!NOTE] 
> A licença premium Azure AD é necessária para as etapas de eliminação.

Azure AD Connect Health for Ative Directory Federation Services (AD FS) e Azure AD Connect (Sync):

1. Abra a lâmina do **Servidor** a partir da lâmina **da Lista** do Servidor selecionando o nome do servidor a ser removido.
2. Na lâmina **do Servidor,** a partir da barra de ação, clique em **Apagar**.
![Screenshot do Azure AD Connect Health apagam](./media/how-to-connect-health-operations/DeleteServer2.png) do servidor
3. Confirme digitando o nome do servidor na caixa de confirmação.
4. Clique em **Eliminar**.

Azure AD Connect Health para serviços de domínio de diretório ativo Azure:

1. Abra o painel de controladores de **domínio.**
2. Selecione o controlador de domínio a remover.
3. A partir da barra de ação, clique em **Eliminar Selecionados**.
4. Confirme a ação para eliminar o servidor.
5. Clique em **Eliminar**.

### <a name="delete-a-service-instance-from-azure-ad-connect-health-service"></a>Eliminar uma instância de serviço do serviço Azure AD Connect Health
Em alguns casos, é melhor remover uma instância de serviço. Aqui está o que precisa de saber para remover uma instância de serviço do serviço Azure AD Connect Health.

Quando estiver a apagar uma instância de serviço, esteja atento ao seguinte:

* Esta ação retira a atual instância de serviço do serviço de monitorização.
* Esta ação não desinstala nem remove o Agente de Saúde de nenhum dos servidores que foram monitorizados como parte desta instância de serviço. Se não tiver desinstalado o Agente de Saúde antes de realizar este passo, poderá ver erros relacionados com o Agente de Saúde nos servidores.
* Todos os dados desta instância de serviço são eliminados de acordo com a política de retenção de dados do Azure.
* Depois de realizar esta ação, se pretender começar a monitorizar o serviço, desinstalar e reinstalar o Agente de Saúde em todos os servidores. Depois de realizar esta ação, se pretender voltar a monitorizar o mesmo servidor, desinstalar, reinstalar e registar o Agente de Saúde nesse servidor.

#### <a name="to-delete-a-service-instance-from-the-azure-ad-connect-health-service"></a>Para eliminar uma instância de serviço do serviço Azure AD Connect Health
1. Abra a lâmina de **serviço** da lâmina **da Lista** de Serviço selecionando o identificador de serviço (nome da fazenda) que pretende remover. 
2. Na lâmina **de serviço,** a partir da barra de ação, clique em **Apagar**. 
![Screenshot da Azure AD Connect Health elimina mato de serviço](./media/how-to-connect-health-operations/DeleteServer.png)
3. Confirme digitando o nome do serviço na caixa de confirmação (por exemplo: sts.contoso.com).
4. Clique em **Eliminar**.
   <br><br>

[//]: # (Início da secção RBAC)
## <a name="manage-access-with-role-based-access-control"></a>Gerir o acesso com controlo de acesso baseado em funções
[O Controlo de Acesso baseado em funções (RBAC)](../../role-based-access-control/role-assignments-portal.md) para a Azure AD Connect Health fornece acesso a utilizadores e grupos que não os administradores globais. O RBAC atribui funções aos utilizadores e grupos pretendidos e fornece um mecanismo para limitar os administradores globais dentro do seu diretório.

### <a name="roles"></a>Funções
A Azure AD Connect Health suporta as seguintes funções incorporadas:

| Função | Permissões |
| --- | --- |
| Proprietário |Os proprietários podem *gerir o acesso* (por exemplo, atribuir uma função a um utilizador ou grupo), ver *toda a informação* (por exemplo, ver alertas) a partir do portal, e *alterar definições* (por exemplo, notificações de e-mail) dentro da Azure AD Connect Health. <br>Por padrão, os administradores globais da Azure AD são atribuídos a esta função, o que não pode ser alterado. |
| Contribuinte |Os contribuintes podem *ver toda a informação* (por exemplo, ver alertas) a partir do portal, e *alterar definições* (por exemplo, notificações de e-mail) dentro da Azure AD Connect Health. |
| Leitor |Os leitores podem *ver toda a informação* (por exemplo, ver alertas) a partir do portal dentro da Azure AD Connect Health. |

Todas as outras funções (como os Administradores de Acesso ao Utilizador ou utilizadores de DevTest Labs) não têm qualquer impacto no acesso dentro do Azure AD Connect Health, mesmo que as funções estejam disponíveis na experiência do portal.

### <a name="access-scope"></a>Âmbito de acesso
A Azure AD Connect Health suporta a gestão do acesso a dois níveis:

* **Todas as instâncias**de serviço : Este é o caminho recomendado na maioria dos casos. Controla o acesso de todas as instâncias de serviço (por exemplo, uma exploração AD FS) em todos os tipos de funções que estão a ser monitorizados pela Azure AD Connect Health.
* **Exemplo de serviço**: Em alguns casos, poderá ser necessário segregar o acesso com base em tipos de funções ou por uma instância de serviço. Neste caso, pode gerir o acesso ao nível da instância de serviço.  

A permissão é concedida se um utilizador final tiver acesso ao nível do diretório ou da instância de serviço.

### <a name="allow-users-or-groups-access-to-azure-ad-connect-health"></a>Permitir aos utilizadores ou grupos o acesso à Azure AD Connect Health
Os seguintes passos mostram como permitir o acesso.
#### <a name="step-1-select-the-appropriate-access-scope"></a>Passo 1: Selecione o âmbito de acesso adequado
Para permitir o acesso de um utilizador ao nível de *todas as instâncias* de serviço dentro da Azure AD Connect Health, abra a lâmina principal na Azure AD Connect Health.<br>

#### <a name="step-2-add-users-and-groups-and-assign-roles"></a>Passo 2: Adicionar utilizadores e grupos e atribuir funções
1. A partir da secção **Configurar,** clique em **Utilizadores**.<br>
   ![Screenshot da barra lateral de recursos Azure AD Connect Health](./media/how-to-connect-health-operations/startRBAC.png)
2. Selecione **Adicionar**.
3. No **painel Selecione um** painel de funções, selecione uma função (por exemplo, **Proprietário).**<br>
   ![Screenshot do Azure AD Connect Health RBAC Utilizadores janela](./media/how-to-connect-health-operations/RBAC_add.png)
4. Digite o nome ou identificador do utilizador ou grupo visado. Ao mesmo tempo, pode selecionar um ou mais utilizadores ou grupos. Clique em **Selecionar**.
   ![Screenshot do Azure AD Connect Health RBAC Utilizadores janela](./media/how-to-connect-health-operations/RBAC_select_users.png)
5. Selecione **OK**.<br>
6. Após a atribuição da função estar completa, os utilizadores e grupos aparecem na lista.<br>
   ![Screenshot da janela de utilizadores do Azure AD Connect Health RBAC, com novos utilizadores em destaque](./media/how-to-connect-health-operations/RBAC_user_list.png)

Agora, os utilizadores e grupos listados têm acesso, de acordo com as suas funções atribuídas.

> [!NOTE]
> * Os administradores globais têm sempre acesso total a todas as operações, mas as contas de administradores globais não estão presentes na lista anterior.
> * A funcionalidade Utilizadores Convidados não é suportada dentro da Azure AD Connect Health.
>
>

#### <a name="step-3-share-the-blade-location-with-users-or-groups"></a>Passo 3: Partilhar a localização da lâmina com utilizadores ou grupos
1. Depois de atribuir permissões, um utilizador pode aceder à Azure AD Connect Health indo [aqui](https://aka.ms/aadconnecthealth).
2. Na lâmina, o utilizador pode fixar a lâmina, ou partes diferentes, no painel de instrumentos. Basta clicar no pin o ícone do painel de **instrumentos.**<br>
   ![Screenshot da lâmina de pino RBAC de ligação a saúde azure, com ícone de pino destacado](./media/how-to-connect-health-operations/RBAC_pin_blade.png)

> [!NOTE]
> Um utilizador com a função Reader atribuída não é capaz de obter a extensão Azure AD Connect Health do Azure Marketplace. O utilizador não pode realizar a operação de "criação" necessária para o fazer. O utilizador ainda pode chegar à lâmina indo para o link anterior. Para posterior utilização, o utilizador pode fixar a lâmina no painel de instrumentos.
>
>

### <a name="remove-users-or-groups"></a>Remover utilizadores ou grupos
Pode remover um utilizador ou um grupo adicionado ao Azure AD Connect Health RBAC. Basta clicar no utilizador ou grupo direito e **selecione Remover**.<br>
![Screenshot da janela de utilizadores do Azure AD Connect Health RBAC, com remover destacado](./media/how-to-connect-health-operations/RBAC_remove.png)

[//]: # (Fim da secção RBAC)

## <a name="next-steps"></a>Passos seguintes
* [Azure AD Connect Health](whatis-hybrid-identity-health.md)
* [Instalação do Agente do Azure AD Connect Health](how-to-connect-health-agent-install.md)
* [Utilizar o Azure AD Connect Health com o AD FS](how-to-connect-health-adfs.md)
* [Utilizar o Azure AD Connect Health para sincronização](how-to-connect-health-sync.md)
* [Utilizar o Azure AD Connect Health com o AD DS](how-to-connect-health-adds.md)
* [Azure AD Connect Health FAQ (FAQ do Azure AD Connect Health)](reference-connect-health-faq.md)
* [Histórico de versões do Azure AD Connect Health](reference-connect-health-version-history.md)
