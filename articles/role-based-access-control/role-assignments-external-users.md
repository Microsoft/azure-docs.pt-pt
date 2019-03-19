---
title: Gerir o acesso aos recursos do Azure para utilizadores externos através do RBAC | Documentos da Microsoft
description: Saiba como gerir o acesso aos recursos do Azure para utilizadores externos à organização utilizar o controlo de acesso baseado em funções (RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 03/20/2018
ms.author: rolyon
ms.reviewer: skwan
ms.custom: it-pro
ms.openlocfilehash: 91548a4df4a77623978ea4bcb214b76427c026a6
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58012017"
---
# <a name="manage-access-to-azure-resources-for-external-users-using-rbac"></a>Gerir o acesso aos recursos do Azure para utilizadores externos através do RBAC

Controlo de acesso baseado em funções (RBAC) permite um melhor gerenciamento de segurança para organizações de grandes porte e para PMEs com colaboradores externos, fornecedores ou freelancers que precisam de aceder a recursos específicos no seu ambiente, mas não necessariamente a toda a trabalhar infraestrutura ou qualquer âmbitos relacionados com faturação. RBAC permite a flexibilidade de proprietário de uma subscrição do Azure geridos pela conta de administrador (função de administrador de serviço ao nível da subscrição) e têm vários utilizadores convidados para trabalhar na mesma subscrição, mas sem quaisquer direitos administrativos para o mesmo .

> [!NOTE]
> Subscrições do Office 365 ou licenças do Azure Active Directory (por exemplo: Acesso ao Azure Active Directory) aprovisionada a partir do Centro de administração não se qualificam para utilizar o RBAC do Microsoft 365.

## <a name="assign-rbac-roles-at-the-subscription-scope"></a>Atribua funções RBAC no âmbito da subscrição

Existem dois exemplos comuns ao RBAC é utilizado (mas não limitado a):

* (Que não faz parte do inquilino do Azure Active Directory do utilizador de admin) que os utilizadores externos das organizações convidado para gerir determinados recursos ou a subscrição completa
* Trabalhar com os utilizadores dentro da organização (fazem parte do inquilino do Azure Active Directory do usuário) mas parte das equipas diferentes ou grupos que precisam de acesso granular para a subscrição completa ou a certos grupos de recursos ou âmbitos de recursos no ambiente

## <a name="grant-access-at-a-subscription-level-for-a-user-outside-of-azure-active-directory"></a>Conceder acesso ao nível da subscrição para um utilizador fora do Azure Active Directory

Funções RBAC podem ser concedidas apenas pelo **proprietários** da subscrição. Por conseguinte, o administrador deve estar conectado como um utilizador com esta função previamente atribuído ou tiver criado a subscrição do Azure.

No portal do Azure, depois de iniciar sessão como administrador, selecione "Subscrições" e escolha aquela pretendido.
![Painel de subscrição no portal do Azure](./media/role-assignments-external-users/0.png) por predefinição, se o utilizador de administrador tiver comprado a subscrição do Azure, o utilizador será apresentado como **administrador de conta**, é a função de subscrição. Para obter mais informações sobre as funções de subscrição do Azure, consulte [adicionar ou alterar os administradores de subscrição do Azure](../billing/billing-add-change-azure-subscription-administrator.md).

Neste exemplo, o utilizador "alflanigan@outlook.com" é o **proprietário** da "Versão de avaliação gratuita" de subscrição no AAD inquilino "Padrão inquilino do Azure". Uma vez que este utilizador é o criador da subscrição do Azure com a inicial Account da Microsoft "Outlook" (Microsoft Account = Outlook, etc. Live) será o nome de domínio predefinido para todos os outros utilizadores adicionados neste inquilino **"\@ alflaniganuoutlook.onmicrosoft.com"**. Por predefinição, a sintaxe do novo domínio é formada pela juntar o nome de domínio e nome de utilizador do utilizador que criou o inquilino e adicionar a extensão **". onmicrosoft.com"**.
Além disso, os utilizadores podem iniciar sessão com um nome de domínio personalizado no inquilino depois de adicionar e verificá-lo para o novo inquilino. Para obter mais informações sobre como verificar um nome de domínio personalizado num inquilino do Azure Active Directory, consulte [adicionar um nome de domínio personalizado ao seu diretório](../active-directory/fundamentals/add-custom-domain.md).

Neste exemplo, o diretório de "Predefinição de inquilino do Azure" contém apenas os utilizadores com o nome de domínio "\@alflanigan.onmicrosoft.com".

Depois de selecionar a subscrição, o utilizador de administrador tem de clicar em **controlo de acesso (IAM)** e, em seguida **adicionar uma nova função**.

![funcionalidade de IAM de controlo de acesso no portal do Azure](./media/role-assignments-external-users/1.png)

![Adicionar novo utilizador na funcionalidade de IAM de controlo de acesso no portal do Azure](./media/role-assignments-external-users/2.png)

A próxima etapa é selecionar quem será atribuída a função RBAC para a função a ser atribuído e o utilizador. Na **função** menu suspenso, o utilizador de administrador verá apenas as funções RBAC incorporadas que estão disponíveis no Azure. Para obter mais explicações de cada função e respetivos âmbitos atribuíveis, consulte [funções incorporadas para recursos do Azure](built-in-roles.md).

O utilizador de administração, em seguida, tem de adicionar o endereço de e-mail do utilizador externo. É o comportamento esperado para o utilizador externo não apareçam no inquilino existente. Depois do utilizador externo foi convidado, ele estará visível sob **subscrições > controlo de acesso (IAM)** com todos os utilizadores atuais que estão atualmente atribuídos uma função RBAC no âmbito da subscrição.

![adicionar permissões à nova função RBAC](./media/role-assignments-external-users/3.png)

![lista de funções RBAC no nível de subscrição](./media/role-assignments-external-users/4.png)

O utilizador "chessercarlton@gmail.com" foi convidado para ser um **proprietário** para a subscrição "Avaliação gratuita". Depois de enviar o convite, o utilizador externo irá receber um e-mail de confirmação com uma ligação de ativação.
![convite por e-mail para a função RBAC](./media/role-assignments-external-users/5.png)

A ser de fora da organização, o novo utilizador não tem quaisquer atributos existentes no diretório "Inquilino predefinido do Azure". Eles serão criados depois do utilizador externo deu consentimento serem registadas no diretório que está associado à subscrição foi atribuído uma função para ele.

![mensagem de convite de e-mail para a função RBAC](./media/role-assignments-external-users/6.png)

Mostra o utilizador externo no inquilino do Azure Active Directory daqui em diante, como utilizador externo e isso pode ser visualizada no portal do Azure.

![portal do Azure do Active Directory do painel do azure de utilizadores](./media/role-assignments-external-users/7.png)

Na **utilizadores** vista, os utilizadores externos podem ser reconhecidos pelo tipo de ícone diferente no portal do Azure.

No entanto, concedendo **proprietário** ou **contribuinte** acesso a um utilizador externo no **subscrição** definir o âmbito, não permite o acesso ao diretório do usuário de administrador, a menos que o **Administrador global** permite que ele. Em Propriedades de utilizador, o **tipo de utilizador**, que tem dois parâmetros comuns, **membro** e **convidado** podem ser identificados. Um membro é um utilizador que está registado no diretório, enquanto um convidado é um utilizador convidado ao diretório de uma origem externa. Para obter mais informações, consulte [como os administradores do Azure Active Directory adicionam utilizadores de colaboração de B2B](../active-directory/active-directory-b2b-admin-add-users.md).

> [!NOTE]
> Certifique-se de que depois de inserir as credenciais no portal, o utilizador externo, seleciona o diretório correto para iniciar sessão no. O mesmo utilizador pode ter acesso a vários diretórios e pode selecionar uma ao clicar no nome de utilizador na parte superior direita no portal do Azure e, em seguida, selecione o diretório adequado na lista pendente.

Ao mesmo tempo que está a ser um convidado no diretório, o utilizador externo pode gerir todos os recursos na subscrição do Azure, mas não é possível aceder ao diretório.

![acesso restringido ao portal do Azure do azure do Active Directory](./media/role-assignments-external-users/9.png)

O Azure Active Directory e uma subscrição do Azure não tem uma relação de pai-filho como os outros recursos do Azure (por exemplo: máquinas virtuais, redes virtuais, aplicações web, armazenamento, etc.) com uma subscrição do Azure. Todos os a última opção é criado, geridos e faturados sob uma subscrição do Azure, ao passo que uma subscrição do Azure é utilizada para gerir o acesso a um diretório do Azure. Para obter mais informações, consulte [como uma subscrição do Azure está relacionada com o Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

De todas as funções RBAC incorporadas, **proprietário** e **contribuinte** oferecem acesso de gestão completa a todos os recursos no ambiente, a diferença é que um Contribuidor não é possível criar e eliminar novas funções do RBAC . Como as outras funções incorporadas **contribuinte de Máquina Virtual** ofereçam completa de gestão acesso apenas aos recursos indicado pelo nome, independentemente do **grupo de recursos** estão a ser criadas em.

Atribuir a função RBAC incorporada de **contribuinte de Máquina Virtual** ao nível da subscrição, significa que a função atribuída ao utilizador:

* Pode ver todas as máquinas virtuais independentemente sua data de implementação e os grupos de recursos que fazem parte do
* Tem acesso de gestão completa para as máquinas virtuais na subscrição
* Não é possível ver outros tipos de recursos na subscrição
* Não é possível operar quaisquer alterações de uma perspectiva de faturação

## <a name="assign-a-built-in-rbac-role-to-an-external-user"></a>Atribuir uma função incorporada do RBAC para um utilizador externo

Para um cenário diferente neste teste, o utilizador externo "alflanigan@gmail.com" é adicionado como um **contribuinte de Máquina Virtual**.

![função incorporada de Contribuidor de máquina virtual](./media/role-assignments-external-users/11.png)

É o comportamento normal para o utilizador externo com esta função incorporada ver e gerir apenas as máquinas virtuais e seus adjacentes do Resource Manager únicos recursos necessários ao implementar. Por predefinição, estas funções limitadas oferecem acesso apenas aos seus recursos correspondente que criou no portal do Azure.

![visão geral da função de contribuinte de máquina virtual no portal do Azure](./media/role-assignments-external-users/12.png)

## <a name="grant-access-at-a-subscription-level-for-a-user-in-the-same-directory"></a>Conceder acesso ao nível da subscrição para um utilizador no mesmo diretório

O fluxo de processo é idêntico ao adicionar um utilizador externo, ambos da perspectiva do administrador conceder a função RBAC, bem como o usuário que está a ser concedido acesso à função. A diferença aqui é que o utilizador convidado não receberá qualquer convites de e-mail como todos os âmbitos de recursos na subscrição vão estar disponíveis no dashboard depois de iniciar sessão.

## <a name="assign-rbac-roles-at-the-resource-group-scope"></a>Atribua funções RBAC no âmbito do grupo de recursos

Atribuir uma função RBAC num **grupo de recursos** âmbito tem um processo idêntico para atribuir a função ao nível da subscrição, para os dois tipos de utilizadores - externos ou internos (parte do mesmo diretório). Os utilizadores que são atribuídos a função RBAC é ver no seu ambiente somente o grupo de recursos foi atribuídos acesso a partir da **grupos de recursos** ícone no portal do Azure.

## <a name="assign-rbac-roles-at-the-resource-scope"></a>Atribua funções RBAC no âmbito do recurso

Atribuir uma função RBAC a um âmbito de recursos no Azure tem um processo idêntico para atribuir a função ao nível da subscrição ou ao nível do grupo de recursos, o mesmo fluxo de trabalho para ambos os cenários a seguir. Novamente, os utilizadores que são atribuídos a função RBAC podem ver apenas os itens que tenham sido atribuídos acesso para, no **todos os recursos** separador ou diretamente no seu dashboard.

É um aspecto importante de RBAC tanto no âmbito do grupo de recursos ou um âmbito de recursos para os utilizadores para se certificar-se de que inicie sessão no diretório correto.

![início de sessão no portal do Azure](./media/role-assignments-external-users/13.png)

## <a name="assign-rbac-roles-for-an-azure-active-directory-group"></a>Atribua funções RBAC para um grupo do Azure Active Directory

Todos os cenários com o RBAC nos três diferentes escopos no Azure oferecem o privilégio de gerenciar, implantar e administrar vários recursos como um utilizador atribuído sem a necessidade de gerir uma subscrição de pessoal. Seja como for, a função RBAC será atribuída para uma subscrição, um grupo de recursos ou um âmbito de recursos, todos os recursos criados mais sobre por que os utilizadores atribuídos são faturados a uma subscrição do Azure onde os utilizadores têm acesso a. Dessa forma, os utilizadores que têm permissões de administrador para essa subscrição do Azure completa de Faturação tem uma descrição geral completa no consumo, independentemente que está a gerir os recursos.

Para organizações maiores, funções RBAC podem ser aplicadas, da mesma forma para grupos do Azure Active Directory, Considerando o ponto de vista do que o utilizador administrador deseja conceder o acesso granular para equipas ou departamentos inteiros, não individualmente para cada utilizador, assim, Considerando ele é muito tempo e o gerenciamento eficiente opção. Para ilustrar neste exemplo, o **contribuinte** função tenha sido adicionada a um dos grupos no inquilino ao nível da subscrição.

![adicionar a função RBAC para grupos do AAD](./media/role-assignments-external-users/14.png)

Estes grupos são grupos de segurança, que são configurados e gerenciados apenas no Azure Active Directory.

