---
title: Configurar uma subscrição do Azure Marketplace para test drives hospedados
description: Explica como configurar uma subscrição do Azure Marketplace para a Dynamics 365 para o Envolvimento do Cliente e Dinâmica 365 para test drives de operações
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: trkeya
ms.author: trkeya
ms.date: 11/09/2020
ms.openlocfilehash: 60eeceac916a7f8c64214b7a74a8cf60fd1ec8ac
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98986129"
---
# <a name="set-up-an-azure-marketplace-subscription-for-hosted-test-drives"></a>Configurar uma subscrição do Azure Marketplace para test drives hospedados

Este artigo explica como configurar uma subscrição do Azure Marketplace e **a Dynamics 365 para o Envolvimento do Cliente** ou Dinâmica **365 para** o ambiente de Operações para test drives.

## <a name="set-up-for-dynamics-365-for-customer-engagement"></a>Configurar para Dynamics 365 para Envolvimento com o Cliente

1. Inscreva-se no [portal Azure](https://portal.azure.com/) com uma conta de Administração.
2. Verifique se está no inquilino associado à sua instância de test drive Dynamics 365, pairando sobre o ícone da sua conta no canto superior direito. Se não estiver no inquilino correto, selecione o ícone da conta para mudar para o inquilino correto.

    :::image type="content" source="./media/test-drive/select-correct-tenant.png" alt-text="Selecionando o inquilino correto.":::

3. Verifique se está disponível a licença **Dynamics 365 Customer Engagement Plan.**

    [![Verificando a licença do plano.](media/test-drive/check-plan-license.png)](media/test-drive/check-plan-license.png#lightbox)

4. Crie uma aplicação Azure Ative Directory (AD) em Azure. O AppSource utilizará esta aplicação para provisões e desprovisionar o utilizador de test drive no seu inquilino.
    1. A partir do painel de filtros, selecione **Azure Ative Directory**.
    2. Selecione **Registos de aplicações**.

        [![Selecionando registos de aplicações.](media/test-drive/app-registrations.png)](media/test-drive/app-registrations.png#lightbox)

    3. Selecione **Novo registo**.
    4. Fornecer um nome de aplicação apropriado.

        :::image type="content" source="./media/test-drive/register-an-application.png" alt-text="Registar uma candidatura.":::

    5. Nos tipos de conta suportados, selecione **Conta em qualquer diretório de organização e contas pessoais da Microsoft.**
    6. Selecione **Criar** e esperar que a sua aplicação seja criada.
    7. Uma vez criada a aplicação, note o ID da **aplicação** exibido no ecrã geral. Mais tarde, necessitará deste valor ao configurar o seu test drive.
    8. Para adicionar um URI de redirecionamento de inclinação nativa, selecione a lâmina **de autenticação.** Na **configuração da Plataforma**, selecione Adicionar o azulejo da aplicação Mobile Desktop da   >    >   Plataforma. Escolha o URI de redirecionamento de **autóctones** e selecione **Configure**.

        :::image type="content" source="./media/test-drive/configure-desktop-devices.png" alt-text="Adicionando um URI de redirecionamento de direcionado nativo.":::

    9. No **Âmbito da Aplicação Manage,** selecione **permissões API**.
    10. **Selecione Adicionar uma permissão** e, em seguida, **Microsoft Graph API**.
    11. Selecione a categoria de permissão **de aplicação** e, em seguida, o **Diretório.Leia.Tudo** e **Diretório.ReadWrite.Todas as** permissões.

        :::image type="content" source="./media/test-drive/microsoft-graph.png" alt-text="Definição das permissões de candidatura.":::

    12. Para adicionar **Dynamics CRM - Acesso de imitação de utilizador** para permitir a lista Azure AD app, selecione **Adicionar novamente permissão.**

        :::image type="content" source="./media/test-drive/request-api-permissions.png" alt-text="Solicitando as permissões de inscrição.":::

    13. Assim que a permissão for adicionada, selecione **o consentimento de administração grant para a Microsoft**.
    14. A partir do alerta de mensagem, selecione **Sim**.

        [![Mostra as permissões de pedidos concedidas com sucesso.](media/test-drive/api-permissions-confirmation-customer.png)](media/test-drive/api-permissions-confirmation-customer.png#lightbox)

    15. Para gerar um segredo para a App AD Azure:
        1. A partir da **Aplicação Manage,** selecione **Certificado e segredos.**
        2. Sob os segredos do Cliente, selecione **Novo segredo de cliente.**
        3. Introduza uma descrição, como *Test Drive,* e selecione uma duração adequada. A unidade de teste quebrará assim que esta Chave expirar, altura em que terá de gerar e fornecer uma nova chave ao AppSource.
        4. **Selecione Adicionar** para gerar o segredo da aplicação Azure. Copie este valor pois será escondido assim que tiver esta lâmina. Mais tarde, necessitará deste valor ao configurar o seu test drive.

            :::image type="content" source="./media/test-drive/add-client-secret.png" alt-text="Adicionando um segredo de cliente.":::

5. Por vezes, demora mais tempo do que o esperado para sincronizar um utilizador do Azure AD para uma instância crm. Para ajudar com isso, adicionámos um processo para forçar o utilizador de sincronização, mas requer que a aplicação AD AZure seja permitida pelo Partner Center. Para isso, consulte [a sincronização do Utilizador para a instância de Envolvimento do Cliente.](https://github.com/microsoft/AppSource/blob/master/Microsoft%20Hosted%20Test%20Drive/CDS_Utility_to_ForceUserSync_in_CRM_Instance.md)
6. Adicione o papel principal do Serviço à aplicação para permitir que a app Azure AD remova os utilizadores do seu inquilino Azure.
    1. Abra uma solicitação de comando PowerShell de nível administrativo.
    2. Install-Module MSOnline (executar este comando se o MSOnline não estiver instalado).
    3. Connect-MsolService (isto mostrará uma janela popup; inscreva-se com o recém-criado inquilino org).
    4. $applicationId = **<YOUR_APPLICATION_ID>.**
    5. $sp = Get-MsolServicePrincipal -AppPrincipalId $applicationId.
    6. Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId $sp. Serviço ObjectId -RoleMemberTypePrincipal.

        :::image type="content" source="./media/test-drive/sign-in-to-account.png" alt-text="A assinar na sua conta.":::

7. Adicione a aplicação Azure acima criada como um utilizador de aplicação à sua instância CRM de test drive.
    1. Adicione um novo utilizador no **Azure Ative Directory**. Apenas os valores **de Nome** e **Nome de Utilizador** (pertencentes ao mesmo inquilino) são necessários para criar este utilizador, deixando os outros campos como padrão. Copie o valor do nome de utilizador.
    2. Inscreva-se na **instância CRM** e selecione **Definição**  >  **de**  >  **Utilizadores** de Segurança .
    3. Alterar a vista para **Utilizadores de Aplicações.**

        :::image type="content" source="./media/test-drive/application-users.png" alt-text="Definição de informação de conta para um utilizador.":::

    4. Adicione um novo utilizador (certifique-se de que o formulário é para UTILIZADOR DE APLICAÇÃO).
    5. Introduza o mesmo nome de utilizador nos campos **'Email' e** **Nome do Utilizador** Primário. Adicione o **Azure ApplicationId** no **ID da aplicação.**
    6. Dê qualquer **nome completo.**
    7. Selecione **Guardar**.
    8. Selecione **Gerir funções**.
    9. Atribua uma função de segurança personalizada ou OOB que contenha ler, escrever e atribuir privilégios de função, tais como *Administrador de Sistema.*

        :::image type="content" source="./media/test-drive/security-roles-selection.png" alt-text="Selecionando os privilégios de papel.":::

    10. Atribua ao utilizador da aplicação a função de segurança personalizada que criou para o seu test drive.

## <a name="set-up-for-dynamics-365-for-operations"></a>Configuração para Dinâmica 365 para Operações

1. Inscreva-se no [portal Azure](https://portal.azure.com/) com uma conta de Administração.
2. Verifique se está no inquilino associado à sua instância de test drive Dynamics 365, pairando sobre o ícone da sua conta no canto superior direito. Se não estiver no inquilino correto, selecione o ícone da conta para mudar para o inquilino correto.

    :::image type="content" source="./media/test-drive/select-correct-tenant.png" alt-text="Selecionando o inquilino correto.":::

3. Crie uma app AD AZure em Azure. O AppSource utilizará esta aplicação para provisões e desprovisionar o utilizador de test drive no seu inquilino.
    1. A partir do painel de filtros, selecione **Azure Ative Directory**.
    2. Selecione **Registos de aplicações**.

        :::image type="content" source="./media/test-drive/app-registrations.png" alt-text="Selecionando um registo de aplicações.":::

    3. Selecione **Novo registo**.
    4. Fornecer um nome de aplicação apropriado.

        :::image type="content" source="./media/test-drive/register-an-application.png" alt-text="Registar uma candidatura.":::

    5. Nos tipos de conta suportados, selecione **Conta em qualquer diretório de organização e contas pessoais da Microsoft.**
    6. Selecione **Criar** e esperar que a sua aplicação seja criada.
    7. Uma vez criada a aplicação, note o ID da **aplicação** exibido no ecrã geral. Mais tarde, necessitará deste valor ao configurar o seu test drive.
    8. No **Âmbito da Aplicação Manage,** selecione **permissões API**.
    9. **Selecione Adicionar uma permissão** e, em seguida, **Microsoft Graph API**.
    10. Selecione a categoria de permissão **de aplicação** e, em seguida, o **Diretório.Leia.Tudo** e **Diretório.ReadWrite.Todas as** permissões.

        :::image type="content" source="./media/test-drive/microsoft-graph.png" alt-text="Definição de permissões de aplicação.":::

    11. Selecione **Adicionar a permissão**.
    12. Assim que a permissão for adicionada, selecione **o consentimento de administração grant para a Microsoft**.
    13. A partir do alerta de mensagem, selecione **Sim**.

        [![Mostra permissões de aplicação concedidas com sucesso.](media/test-drive/api-permissions-confirmation-operations.png)](media/test-drive/api-permissions-confirmation-operations.png#lightbox)

    14. Para gerar um segredo para a App AD Azure:
        1. A partir da **Aplicação Manage,** selecione **Certificado e segredos.**
        2. Sob os segredos do Cliente, selecione **Novo segredo de cliente.**
        3. Introduza uma descrição, como *Test Drive,* e selecione uma duração adequada. A unidade de teste quebrará assim que esta Chave expirar, altura em que terá de gerar e fornecer uma nova chave ao AppSource.
        4. **Selecione Adicionar** para gerar o segredo da aplicação Azure. Copie este valor pois será escondido assim que tiver esta lâmina. Mais tarde, necessitará deste valor ao configurar o seu test drive.

            :::image type="content" source="./media/test-drive/add-client-secret.png" alt-text="Adicionando um segredo de cliente.":::

4. Adicione o papel principal do Serviço à aplicação para permitir que a app Azure AD remova os utilizadores do seu inquilino Azure.
    1. Abra uma solicitação de comando PowerShell de nível administrativo.
    2. Install-Module MSOnline (executar este comando se o MSOnline não estiver instalado).
    3. Connect-MsolService (isto mostrará uma janela popup; inscreva-se com o recém-criado inquilino org).
    4. $applicationId = **<YOUR_APPLICATION_ID>.**
    5. $sp = Get-MsolServicePrincipal -AppPrincipalId $applicationId.
    6. Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId $sp. Serviço ObjectId -RoleMemberTypePrincipal.

        :::image type="content" source="./media/test-drive/sign-in-to-account.png" alt-text="A assinar na sua conta.":::

5. Agora adicione a app acima à **Dynamics 365 para Operações** para permitir que a app gere os utilizadores.
    1. Encontre a sua **Dinâmica 365 para operações** por exemplo.
    2. Do canto superior esquerdo, clique no menu de três linhas (hambúrguer).
    3. Selecione **Administração do Sistema**.
    4. Selecione **aplicações de Diretório Ativo Azure**.
    5. Selecione **+ Novo**.
    6. Insira o **ID do Cliente da app AZure AD** que vai realizar as ações em nome.

    > [!NOTE]
    > O ID do utilizador em nome de quem as ações serão executadas (normalmente o Administrador do Sistema da instância ou um utilizador que tem privilégios em adicionar outros utilizadores).

    [![O ID do utilizador em nome de quem as ações serão executadas (normalmente o Administrador do Sistema da instância ou um utilizador que tem privilégios em adicionar outros utilizadores).](media/test-drive/system-admin-user-id.png)](media/test-drive/system-admin-user-id.png#lightbox)
<!--
## Next steps

- [Commercial marketplace partner and customer usage attribution](azure-partner-customer-usage-attribution.md) -->
