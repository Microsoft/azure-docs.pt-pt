---
title: Azure Ative Directory Connect Health operações
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
ms.topic: how-to
ms.date: 07/18/2017
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 91b7bd6d05ee0b992ee166b9acb13aff2e605a2c
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94961010"
---
# <a name="azure-active-directory-connect-health-operations"></a>Azure Ative Directory Connect Health operações
Este tópico descreve as várias operações que pode realizar utilizando o Azure Ative Directory (Azure AD) Connect Health.

## <a name="enable-email-notifications"></a>Ativar notificações de e-mail
Pode configurar o serviço Azure AD Connect Health para enviar notificações por e-mail quando os alertas indicam que a sua infraestrutura de identidade não é saudável. Isto ocorre quando um alerta é gerado, e quando é resolvido.

![Screenshot de Azure AD Connect Health definições de notificação de e-mail](./media/how-to-connect-health-operations/email_noti_discover.png)

> [!NOTE]
> As notificações por e-mail são ativadas por padrão.
>

### <a name="to-enable-azure-ad-connect-health-email-notifications"></a>Para ativar notificações de email do Azure AD Connect Health
1. Abra a lâmina **Alertas** para o serviço para o qual pretende receber a notificação por e-mail.
2. A partir da barra de ação, clique em **Definições de Notificação**.
3. No comutador de notificações por e-mail, selecione **ON**.
4. Selecione a caixa de verificação se quiser que todos os administradores globais recebam notificações por e-mail.
5. Se pretender receber notificações de e-mail em quaisquer outros endereços de e-mail, especifique-as na caixa **de Destinatários de Email Adicionais.** Para remover um endereço de e-mail desta lista, clique com o botão direito para entrar e selecione **Delete**.
6. Para finalizar as alterações, clique em **Guardar**. As alterações só fazem efeito depois de poupar.

>[!NOTE] 
> Quando existem problemas de processamento de pedidos de sincronização no nosso serviço de backend, este serviço envia um e-mail de notificação com os detalhes do erro para o endereço de e-mail de contacto administrativo(es) do seu inquilino. Ouvimos feedback dos clientes que em certos casos o volume destas mensagens é proibitivamente grande, pelo que estamos a mudar a forma como enviamos estas mensagens. 
>
> Em vez de enviar uma mensagem para cada erro de sincronização sempre que ocorrer, enviaremos uma digestão diária de todos os erros que o serviço de backend devolveu. Isto permite que os clientes processem estes erros de uma forma mais eficiente e reduz o número de mensagens de erro duplicadas.
>
> Planeamos que esta mudança seja implementada no dia 15 de janeiro de 2020.

## <a name="delete-a-server-or-service-instance"></a>Excluir um servidor ou instância de serviço

>[!NOTE] 
> A licença AD premium Azure é necessária para as etapas de eliminação.

Em alguns casos, é melhor remover um servidor de ser monitorizado. Aqui está o que precisa de saber para remover um servidor do serviço Azure AD Connect Health.

Quando estiver a apagar um servidor, esteja atento ao seguinte:

* Esta ação deixa de recolher quaisquer dados adicionais desse servidor. Este servidor é removido do serviço de monitorização. Após esta ação, não é possível visualizar novos alertas, monitorização ou utilização de dados de análise para este servidor.
* Esta ação não desinstala o Agente de Saúde do seu servidor. Se não tiver desinstalado o Health Agent antes de realizar este passo, poderá ver erros relacionados com o Agente de Saúde no servidor.
* Esta ação não elimina os dados já recolhidos deste servidor. Estes dados são eliminados de acordo com a política de retenção de dados Azure.
* Depois de efetuar esta ação, se pretender voltar a monitorizar o mesmo servidor, tem de desinstalar e reinstalar o Health Agent neste servidor.

### <a name="delete-a-server-from-the-azure-ad-connect-health-service"></a>Excluir um servidor do serviço Azure AD Connect Health

>[!NOTE] 
> A licença AD premium Azure é necessária para as etapas de eliminação.

Azure AD Connect Health for Ative Directory Federation Services (AD FS) e Azure AD Connect (Sync):

1. Abra a lâmina do **servidor** a partir da lâmina **'Lista de servidores',** selecionando o nome do servidor a ser removido.
2. Na lâmina **do Servidor,** a partir da barra de ação, clique em **Eliminar**.
![Screenshot do Azure AD Connect Health delete servidor](./media/how-to-connect-health-operations/DeleteServer2.png)
3. Confirme digitando o nome do servidor na caixa de confirmação.
4. Clique em **Eliminar**.

Azure AD Connect Health for Azure Ative Directory Domain Services:

1. Abra o painel **de controladores de domínio.**
2. Selecione o controlador de domínio a ser removido.
3. A partir da barra de ação, clique **em Eliminar Selecionado.**
4. Confirme a ação para eliminar o servidor.
5. Clique em **Eliminar**.

### <a name="delete-a-service-instance-from-azure-ad-connect-health-service"></a>Eliminar uma instância de serviço do serviço Azure AD Connect Health
Em alguns casos, é melhor remover uma instância de serviço. Aqui está o que precisa de saber para remover uma instância de serviço do serviço Azure AD Connect Health.

Quando estiver a apagar uma instância de serviço, esteja ciente do seguinte:

* Esta ação remove a atual instância de serviço do serviço de monitorização.
* Esta ação não desinstala ou remove o Agente de Saúde de nenhum dos servidores que foram monitorizados como parte desta instância de serviço. Se não tiver desinstalado o Agente de Saúde antes de realizar este passo, poderá ver erros relacionados com o Agente de Saúde nos servidores.
* Todos os dados desta instância de serviço são eliminados de acordo com a política de retenção de dados Azure.
* Depois de efetuar esta ação, se pretender começar a monitorizar o serviço, desinstale e reinstale o Health Agent em todos os servidores. Depois de efetuar esta ação, se pretender voltar a monitorizar o mesmo servidor, desinstale, reinstale e registe o Agente de Saúde nesse servidor.

#### <a name="to-delete-a-service-instance-from-the-azure-ad-connect-health-service"></a>Para eliminar uma instância de serviço do serviço Azure AD Connect Health
1. Abra a lâmina de **serviço** da lâmina **da Lista** de Serviço selecionando o identificador de serviço (nome da quinta) que pretende remover. 
2. Na lâmina **de serviço,** a partir da barra de ação, clique em **Eliminar**. 
![Screenshot do serviço Azure AD Connect Health](./media/how-to-connect-health-operations/DeleteServer.png)
3. Confirme digitando o nome de serviço na caixa de confirmação (por exemplo: sts.contoso.com).
4. Clique em **Eliminar**.
   <br><br>

[//]: # (Início da secção RBAC)
## <a name="manage-access-with-azure-rbac"></a>Gerir o acesso com o Azure RBAC
[O controlo de acesso baseado em funções Azure (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md) para a Azure AD Connect Health proporciona acesso a utilizadores e grupos que não os administradores globais. O Azure RBAC atribui funções aos utilizadores e grupos pretendidos e fornece um mecanismo para limitar os administradores globais dentro do seu diretório.

### <a name="roles"></a>Funções
A Azure AD Connect Health suporta as seguintes funções incorporadas:

| Função | Permissões |
| --- | --- |
| Proprietário |Os proprietários podem gerir o *acesso* (por exemplo, atribuir uma função a um utilizador ou grupo), *ver todas as informações* (por exemplo, ver alertas) a partir do portal e *alterar definições* (por exemplo, notificações de email) dentro do Azure AD Connect Health. <br>Por padrão, os administradores globais da Azure AD são atribuídos a esta função, e isso não pode ser alterado. |
| Contribuinte |Os contribuintes podem *visualizar todas as informações* (por exemplo, ver alertas) a partir do portal e *alterar definições* (por exemplo, notificações de e-mail) dentro do Azure AD Connect Health. |
| Leitor |Os leitores podem *ver todas as informações* (por exemplo, ver alertas) a partir do portal dentro do Azure AD Connect Health. |

Todas as outras funções (como Administradores de Acesso ao Utilizador ou Utilizadores de Laboratórios DevTest) não têm qualquer impacto no acesso dentro do Azure AD Connect Health, mesmo que as funções estejam disponíveis na experiência do portal.

### <a name="access-scope"></a>Âmbito de acesso
A Azure AD Connect Health suporta a gestão do acesso a dois níveis:

* **Todas as instâncias de serviço**: Este é o caminho recomendado na maioria dos casos. Controla o acesso a todas as instâncias de serviço (por exemplo, uma exploração AD FS) em todos os tipos de funções que estão a ser monitorizados pela Azure AD Connect Health.
* **Caso de serviço**: Em alguns casos, poderá ser necessário segregar o acesso com base em tipos de funções ou por uma instância de serviço. Neste caso, pode gerir o acesso ao nível da instância de serviço.  

A permissão é concedida se um utilizador final tiver acesso ao nível do diretório ou da instância de serviço.

### <a name="allow-users-or-groups-access-to-azure-ad-connect-health"></a>Permitir aos utilizadores ou grupos o acesso ao Azure AD Connect Health
Os passos a seguir mostram como permitir o acesso.
#### <a name="step-1-select-the-appropriate-access-scope"></a>Passo 1: Selecione o âmbito de acesso adequado
Para permitir o acesso do utilizador a *todos os casos* de serviço dentro da Azure AD Connect Health, abra a lâmina principal em Azure AD Connect Health.<br>

#### <a name="step-2-add-users-and-groups-and-assign-roles"></a>Passo 2: Adicionar utilizadores e grupos e atribuir funções
1. A partir da secção **Configurar,** clique nos **Utilizadores**.<br>
   ![Screenshot da barra lateral de recursos Azure AD Connect Health](./media/how-to-connect-health-operations/startRBAC.png)
2. Selecione **Adicionar**.
3. No **painel de funções Select,** selecione uma função (por exemplo, **Proprietário).**<br>
   ![Screenshot do menu de configuração Azure AD Connect Health e Azure RBAC](./media/how-to-connect-health-operations/RBAC_add.png)
4. Digite o nome ou identificador do utilizador ou grupo visado. Pode selecionar um ou mais utilizadores ou grupos ao mesmo tempo. Clique em **Selecionar**.
   ![Screenshot da lista de funções Azure AD Connect Health e Azure](./media/how-to-connect-health-operations/RBAC_select_users.png)
5. Selecione **OK**.<br>
6. Após a atribuição da função estar concluída, os utilizadores e grupos aparecem na lista.<br>
   ![Screenshot do Azure AD Connect Health e Azure RBAC e novos utilizadores em destaque](./media/how-to-connect-health-operations/RBAC_user_list.png)

Agora os utilizadores e grupos listados têm acesso, de acordo com as suas funções atribuídas.

> [!NOTE]
> * Os administradores globais têm sempre acesso total a todas as operações, mas as contas globais de administrador não estão presentes na lista anterior.
> * A funcionalidade Utilizadores convidados não é suportada no Azure AD Connect Health.
>
>

#### <a name="step-3-share-the-blade-location-with-users-or-groups"></a>Passo 3: Partilhar a localização da lâmina com utilizadores ou grupos
1. Depois de atribuir permissões, um utilizador pode aceder ao Azure AD Connect Health indo [aqui](https://aka.ms/aadconnecthealth).
2. Na lâmina, o utilizador pode fixar a lâmina, ou diferentes partes da sua, ao painel de instrumentos. Basta clicar no **ícone pin para painel de instrumentos.**<br>
   ![Screenshot de Azure AD Connect Health e Azure RBAC pin blade, com ícone pino em destaque](./media/how-to-connect-health-operations/RBAC_pin_blade.png)

> [!NOTE]
> Um utilizador com a função Reader atribuída não é capaz de obter a extensão Azure AD Connect Health do Azure Marketplace. O utilizador não pode efetuar a operação necessária para o fazer. O utilizador ainda pode chegar à lâmina indo para o link anterior. Para utilização posterior, o utilizador pode fixar a lâmina no painel de instrumentos.
>
>

### <a name="remove-users-or-groups"></a>Remover utilizadores ou grupos
Pode remover um utilizador ou um grupo adicionado à Azure AD Connect Health e ao Azure RBAC. Basta clicar no utilizador ou grupo e selecionar **Remover**.<br>
![Screenshot de Azure AD Connect Health e Azure RBAC com Remove em destaque](./media/how-to-connect-health-operations/RBAC_remove.png)

[//]: # (Fim da secção RBAC)

## <a name="next-steps"></a>Passos seguintes
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Instalação do Agente de Saúde Azure AD Connect](how-to-connect-health-agent-install.md)
* [Utilizar o Azure AD Connect Health com o AD FS](how-to-connect-health-adfs.md)
* [Usando Azure AD Connect Health para sincronização](how-to-connect-health-sync.md)
* [Utilizar o Azure AD Connect Health com o AD DS](how-to-connect-health-adds.md)
* [FAQ do Azure AD Connect Health](reference-connect-health-faq.md)
* [Histórico das versões do Azure AD Connect Health](reference-connect-health-version-history.md)