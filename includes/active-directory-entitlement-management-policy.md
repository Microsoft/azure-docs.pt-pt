---
title: incluir ficheiro
description: incluir ficheiro
services: active-directory
author: rolyon
ms.service: active-directory
ms.topic: include
ms.date: 04/16/2019
ms.author: rolyon
ms.custom: include file
ms.openlocfilehash: ef8155cda6c31a63204af80ca091a13bb16a687d
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2019
ms.locfileid: "64866418"
---
### <a name="policy-for-users-in-your-directory"></a>Política: Para os utilizadores no seu diretório

Siga estes passos, se pretender que a política para utilizadores e grupos no seu diretório que podem solicitar este pacote de acesso.

1. Na **usuários que podem pedir acesso** secção, selecione **para os utilizadores no seu diretório**.

1. Na **selecionar utilizadores e grupos** secção, clique em **adicionar utilizadores e grupos**.

1. No painel de grupos e selecionar utilizadores, selecione os utilizadores e grupos que pretende adicionar.

    ![Pacote de acesso - política - selecione utilizadores e grupos](./media/active-directory-entitlement-management-policy/policy-select-users-groups.png)

1. Clique em **selecione** para adicionar os utilizadores e grupos.

1. Avance para o [política: Pedir](#policy-request) secção.

### <a name="policy-for-users-not-in-your-directory"></a>Política: Para os utilizadores não no seu diretório

Siga estes passos, se pretender que a política para utilizadores não no seu diretório que podem solicitar este pacote de acesso. Diretórios devem ser configurados para permitir a **restrições de colaboração de relações organizacionais** definições.

1. Na **usuários que podem pedir acesso** secção, selecione **para os utilizadores não no seu diretório**.

1. Na **selecione externo do Azure AD directory** secção, clique em **adicionar diretórios**.

1. Introduza um nome de domínio e a pesquisa um externas diretório do Azure AD.

1. Certifique-se de que é o diretório correto pelo nome de diretório fornecido e o domínio inicial.

    > [!NOTE]
    > Todos os utilizadores do diretório poderá pedir este pacote de acesso. Isto inclui os utilizadores de todos os subdomínios associados ao diretório, não apenas o domínio utilizado na pesquisa.

    ![Pacote - acesso a diretórios de política de seleção](./media/active-directory-entitlement-management-policy/policy-select-directories.png)

1. Clique em **adicionar** para adicionar o diretório.

1. Repita este passo para adicionar todos os diretórios mais.

1. Depois de adicionar todos os diretórios que pretende incluir na política, clique em **selecione**.

1. Avance para o [política: Pedir](#policy-request) secção.

### <a name="policy-none-administrator-direct-assignments-only"></a>Política: Nenhum (administrador direto atribuições apenas)

Siga estes passos, se pretender que a política para ignorar pedidos de acesso e permitir que os administradores atribuir diretamente a utilizadores específicos para o pacote de acesso. Os usuários não terão que pedir o pacote de acesso. Pode ainda configurar definições de expiração, mas não há nenhuma definição de pedido.

1. Na **os utilizadores que podem pedir acesso** secção, selecione **None (atribuições diretas administrador apenas**.

    Depois de criar o pacote de acesso, pode atribuir utilizadores internos e externos específicos diretamente para o pacote de acesso. Se especificar um utilizador externo, será criada uma conta de utilizador convidado no seu diretório.

1. Avance para o [política: Expiração](#policy-expiration) secção.

### <a name="policy-request"></a>Política: Pedir

Na seção Request, especifique as definições de aprovação quando os utilizadores solicitem o pacote de acesso.

1. Para exigir a aprovação para pedidos de utilizadores selecionados, defina o **exigem a aprovação** alternar para **Sim**. Para que as solicitações aprovadas automaticamente, defina o seletor para **não**.

1. Se necessitar de aprovação, no **selecionar aprovadores** secção, clique em **adicionar aprovadores**.

1. No painel de aprovadores selecione, selecione um ou mais utilizadores e/ou grupos para ser aprovadores.

    Apenas um dos aprovadores selecionados tem de aprovar um pedido. Aprovação de todos os aprovadores não é necessária. A decisão de aprovação é baseada em qualquer aprovador revê o pedido pela primeira vez.

    ![Pacote de acesso - política - selecionar aprovadores](./media/active-directory-entitlement-management-policy/policy-select-approvers.png)

1. Clique em **selecione** para adicionar os aprovadores.

1. Clique em **Mostrar pedido definições avançadas** para mostrar as definições adicionais.

    ![Pacote - acesso a diretórios de política de seleção](./media/active-directory-entitlement-management-policy/policy-advanced-request.png)

1. Para exigir que os utilizadores fornecer uma justificação para pedir o pacote de acesso, defina **requerer justificação** ao **Sim**.

1. Para exigir que o aprovador fornecer uma justificação para aprovar um pedido para o pacote de acesso, defina **requerer justificação de aprovador** ao **Sim**.

1. Na **tempo limite do pedido de aprovação (dias)** caixa, especifique a quantidade de tempo aprovadores para rever um pedido. Se não os aprovadores revê-lo neste número de dias, o pedido expira e o utilizador terá de enviar outro pedido para o pacote de acesso.

### <a name="policy-expiration"></a>Política: Expiração

Na secção de expiração, especifique quando expira a atribuição de um utilizador para o pacote de acesso.

1. Na **expiração** secção, defina **pacote de acesso expira** para **na data**, **número de dias**, ou **Never**.

    Para **numa data**, selecione uma data de expiração no futuro.

    Para **número de dias**, especifique um número entre 0 e 3660 dias.

    Com base na sua seleção, atribuição de um utilizador para o pacote de acesso expira numa determinada data, um determinado número de dias após serem aprovados, ou nunca.

1. Clique em **Mostrar definições de expiração avançadas** para mostrar as definições adicionais.

1. Para permitir que o utilizador expandir as suas atribuições, defina **permitir que os utilizadores estender o acesso** ao **Sim**.

    Se as extensões são permitidas na política, o utilizador receberá uma mensagem de e-mail 14 dias e também 1 dia antes da respetiva atribuição de pacote de acesso é definida para expirar a pedir aos mesmos para expandir a atribuição.

    ![Pacote de acesso - definições de política de expiração](./media/active-directory-entitlement-management-policy/policy-expiration.png)

### <a name="policy-enable-policy"></a>Política: Activar política

1. Se pretender que o pacote de acesso que se tornarão imediatamente disponíveis para os utilizadores a política, clique em **Sim** para ativar a política.

    Pode sempre ativá-la no futuro após concluir a criação do pacote de acesso.

    ![Pacote de acesso - a definição de política de política de ativação](./media/active-directory-entitlement-management-policy/policy-enable.png)

1. Clique em **próxima** ou **criar**.
