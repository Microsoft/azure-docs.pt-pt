---
title: 'Tutorial: configurar o provisionamento de entrada do SuccessFactors no Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o provisionamento de entrada do SuccessFactors para o Azure AD
services: active-directory
author: cmmdesai
documentationcenter: na
manager: jodadzie
ms.assetid: fac4f61e-d942-4429-a298-9ba74db95077
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2019
ms.author: chmutali
ms.openlocfilehash: 09501a80d6ddcbbc9fa6cc08e36f47beb13d1663
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063227"
---
# <a name="tutorial-configure-sap-successfactors-to-azure-ad-user-provisioning-preview"></a>Tutorial: configurar o SAP SuccessFactors para o provisionamento de usuário do Azure AD (versão prévia)
O objetivo deste tutorial é mostrar as etapas que você precisa executar para provisionar dados de trabalho do SuccessFactors Employee central no Azure Active Directory, com write-back opcional de endereço de email para SuccessFactors. Esta integração encontra-se na pré-visualização pública e suporta a recuperação de mais de [70 atributos](../app-provisioning/sap-successfactors-attribute-reference.md) de utilizadores da SuccessFactors Employee Central. 

>[!NOTE]
>Use este tutorial se os usuários que você deseja provisionar do SuccessFactors forem usuários somente na nuvem que não precisam de uma conta do AD local. Se os utilizadores necessitarem apenas de uma conta AD no local ou da conta AD e Azure AD, consulte o tutorial sobre configurar o [SAP SuccessFactors para](sap-successfactors-inbound-provisioning-tutorial.md#overview) o fornecimento de utilizadores de Diretório Ativo. 

## <a name="overview"></a>Descrição geral

O serviço de prestação de [utilizadores do Azure Ative Directory](../app-provisioning/user-provisioning.md) integra-se com a [Central de Colaboradores SuccessFactors](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) de forma a gerir o ciclo de vida de identidade dos utilizadores. 

Os fluxos de trabalho de provisionamento de usuário SuccessFactors com suporte do serviço de provisionamento de usuário do Azure AD habilitam a automação dos seguintes cenários de gerenciamento de ciclo de vida de identidade e recursos humanos:

* **Contratação** de novos colaboradores - Quando um novo colaborador é adicionado ao SuccessFactors, uma conta de utilizador é automaticamente criada no Diretório Ativo do Azure e opcionalmente office 365 e [outras aplicações SaaS suportadas pela Azure AD,](../app-provisioning/user-provisioning.md)com redação do endereço de e-mail para SuccessFactors.

* **Atributo seletiva e atualizações** de perfil - Quando um registo de empregados é atualizado em SuccessFactors (como o seu nome, título ou gestor), a sua conta de utilizador será automaticamente atualizada do Azure Ative Directory e opcionalmente office 365 e [outras aplicações SaaS suportadas pela Azure AD](../app-provisioning/user-provisioning.md).

* **Rescisões** de funcionários - Quando um empregado é despedido em SuccessFactors, a sua conta de utilizador é automaticamente desativada no Diretório Ativo Azure e opcionalmente office 365 e [outras aplicações SaaS suportadas pela Azure AD](../app-provisioning/user-provisioning.md).

* **Recontratações** de empregados - Quando um empregado é recontratado em SuccessFactors, a sua conta antiga pode ser automaticamente reativada ou reaprovisionada (dependendo da sua preferência) para o Diretório Ativo Azure e opcionalmente office 365 e [outras aplicações SaaS suportadas pela Azure AD](../app-provisioning/user-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>A qual esta solução de provisionamento de usuário mais adequada?

Esse SuccessFactors para Azure Active Directory solução de provisionamento de usuário é ideal para:

* Organizações que desejam uma solução predefinida baseada em nuvem para provisionamento de usuário SuccessFactors

* Organizações que exigem o provisionamento direto de usuários do SuccessFactors para o Azure Active Directory

* Organizações que exigem que os utilizadores sejam aprovisionados utilizando dados obtidos a partir da Central de Empregados de [SucessoFactors (CE)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)

* Organizações que usam o Office 365 para email

## <a name="solution-architecture"></a>Arquitetura de Soluções

Esta seção descreve a arquitetura da solução de provisionamento de usuário de ponta a ponta para usuários somente em nuvem. Há dois fluxos relacionados:

* **Fluxo de dados de RH autoritário – de SuccessFactors a Diretório Ativo Azure:** Neste fluxo os eventos de trabalhadores (tais como Novas Contratações, Transferências, Rescisões) ocorrem primeiro na nuvem SuccessFactors Employee Central e, em seguida, os dados do evento fluem para o Diretório Ativo Azure. Dependendo do evento, ele pode levar a criar/atualizar/habilitar/desabilitar operações no Azure AD.
* **Fluxo de Writeback por e-mail – do Diretório Ativo no local para SuccessFactors:** Uma vez que a criação da conta esteja completa no Diretório Ativo Do Azure, o valor do atributo de e-mail ou UPN gerado em Azure AD pode ser reescrito para SuccessFactors.

  ![Descrição geral](./media/sap-successfactors-inbound-provisioning/sf2aad-overview.png)

### <a name="end-to-end-user-data-flow"></a>Fluxo de dados do usuário de ponta a ponta

1. A equipe de RH executa transações de trabalho (junções/movimentadores/pertraçãos ou novas contratações/transferências/encerramentos) no SuccessFactors Employee central
2. O serviço de provisionamento do Azure AD executa sincronizações agendadas de identidades do SuccessFactors EC e identifica as alterações que precisam ser processadas para sincronização com Active Directory locais.
3. O serviço de provisionamento do Azure AD determina a alteração e invoca a operação criar/atualizar/habilitar/desabilitar para o usuário no Azure AD.
4. Se a [aplicação SuccessFactors Writeback](sap-successfactors-writeback-tutorial.md) estiver configurada, o endereço de e-mail do utilizador é recuperado a partir de Azure AD. 
5. O serviço de provisionamento do Azure AD grava o atributo de email de volta em SuccessFactors, com base no atributo correspondente usado.

## <a name="planning-your-deployment"></a>Planejando a implantação

A configuração do provisionamento de usuário controlado por RH na nuvem do SuccessFactors para o Azure AD requer um planejamento considerável que abrange diferentes aspectos, como:

* Determinando a ID de correspondência 
* Mapeamento de atributos
* Transformação de atributo 
* Filtros de âmbito

Consulte o plano de [implantação](../app-provisioning/plan-cloud-hr-provision.md) de RH em nuvem para diretrizes abrangentes em torno destes tópicos. 

## <a name="configuring-successfactors-for-the-integration"></a>Configurando o SuccessFactors para a integração

Um requisito comum de todos os conectores de provisionamento do SuccessFactors é que eles exigem credenciais de uma conta SuccessFactors com as permissões corretas para invocar as APIs OData do SuccessFactors. Esta seção descreve as etapas para criar a conta de serviço no SuccessFactors e conceder as permissões apropriadas. 

* [Criar/identificar conta de utilizador da API em SuccessFactors](#createidentify-api-user-account-in-successfactors)
* [Criar um papel de permissões DaPI](#create-an-api-permissions-role)
* [Criar um Grupo de Permissão para o utilizador da API](#create-a-permission-group-for-the-api-user)
* [Papel de permissão de concessão para o Grupo de Permissão](#grant-permission-role-to-the-permission-group)

### <a name="createidentify-api-user-account-in-successfactors"></a>Criar/identificar conta de usuário de API no SuccessFactors
Trabalhe com sua equipe de administração do SuccessFactors ou com o parceiro de implementação para criar ou identificar uma conta de usuário no SuccessFactors que será usada para invocar as APIs OData. As credenciais de nome de usuário e senha desta conta serão necessárias ao configurar os aplicativos de provisionamento no Azure AD. 

### <a name="create-an-api-permissions-role"></a>Criar uma função de permissões de API

* Faça logon no SAP SuccessFactors com uma conta de usuário que tenha acesso ao centro de administração.
* Procure *funções*de permissão de gestão e, em seguida, selecione **'Gerir funções de permissão'** a partir dos resultados da pesquisa.
  ![gerir papéis de permissão](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)
* A partir da Lista de Papéis de Permissão, clique em **Criar Novo**.
  > [!div class="mx-imgBorder"]
  > ![criar novos](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png) de permissão
* Adicione um **nome** e **descrição** para o novo papel de permissão. O nome e a descrição devem indicar que a função é para permissões de uso da API.
  > [!div class="mx-imgBorder"]
  > ![permissão detalhe](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)
* Sob as definições de Permissão, clique em **Permissão...** em seguida, percorra a lista de permissão e clique em **Gerir Ferramentas**de Integração . Verifique a caixa para permitir que o **Administrador aceda à API OData através**da autenticação básica .
  > [!div class="mx-imgBorder"]
  > ![Gerir ferramentas de integração](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)
* Desloque-se para baixo na mesma caixa e selecione **API Central do Empregado**. Adicione permissões conforme mostrado abaixo para ler usando a API do ODATA e editar usando a API do ODATA. Selecione a opção Editar se você planeja usar a mesma conta para o SuccessFactors cenário de write-back. 
  > [!div class="mx-imgBorder"]
  > ![Ler permissões](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)
* Clique em **Done**. Clique em **Guardar Alterações**.

### <a name="create-a-permission-group-for-the-api-user"></a>Criar um grupo de permissões para o usuário da API

* No Centro de Administração SuccessFactors, procure grupos de *permissão de gestão*e, em seguida, selecione **Manage Permission Groups** a partir dos resultados da pesquisa.
  > [!div class="mx-imgBorder"]
  > ![Gerir grupos de permissão](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)
* A partir da janela 'Gerir grupos de permissão', clique em **Criar Novo**.
  > [!div class="mx-imgBorder"]
  > ![Adicionar novos](./media/sap-successfactors-inbound-provisioning/create-new-group.png) de grupo
* Adicione um nome de grupo para o novo grupo. O nome do grupo deve indicar que o grupo é para usuários de API.
  > [!div class="mx-imgBorder"]
  > ![nome do grupo de permissão](./media/sap-successfactors-inbound-provisioning/permission-group-name.png)
* Adicione membros ao grupo. Por exemplo, pode selecionar o **username** do menu de entrega do People Pool e, em seguida, introduzir o nome de utilizador da conta API que será usada para a integração. 
  > [!div class="mx-imgBorder"]
  > ![Adicionar membros do grupo](./media/sap-successfactors-inbound-provisioning/add-group-members.png)
* Clique em **Feito** para terminar de criar o Grupo de Permissões.

### <a name="grant-permission-role-to-the-permission-group"></a>Conceder a função de permissão ao grupo de permissões

* No SuccessFactors Admin Center, procure funções de permissão de *gestão*e, em seguida, selecione **'Gerir funções de permissão'** a partir dos resultados da pesquisa.
* A partir da Lista de Papéis de **Permissão,** selecione o papel que criou para permissões de utilização da API.
* Sob **o Grant este papel para...** , clique em **Adicionar...** botão.
* Selecione **Grupo de Permissões...** a partir do menu suspenso e, em seguida, clique em **Select...** para abrir a janela Grupos para pesquisar e selecionar o grupo acima criado. 
  > [!div class="mx-imgBorder"]
  > ![Adicionar](./media/sap-successfactors-inbound-provisioning/add-permission-group.png) de grupo de permissão
* Examine a concessão da função de permissão para o grupo de permissões. 
  > [!div class="mx-imgBorder"]
  > ![Papel de Permissão e](./media/sap-successfactors-inbound-provisioning/permission-role-group.png) de pormenor do Grupo
* Clique em **Guardar Alterações**.

## <a name="configuring-user-provisioning-from-successfactors-to-azure-ad"></a>Configurando o provisionamento de usuário do SuccessFactors para o Azure AD

Esta seção fornece etapas para o provisionamento de contas de usuário do SuccessFactors para o Azure AD.

* [Adicione a aplicação de conector de provisionamento e configure a conectividade aos SuccessFactors](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [Configure mapeamentos de atributos](#part-2-configure-attribute-mappings)
* [Ativar e lançar o fornecimento de utilizadores](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>Parte 1: Adicionar o aplicativo do conector de provisionamento e configurar a conectividade com o SuccessFactors

**Para configurar o fornecimento de SuccessFactors para AD Azure:**

1. Ir para <https://portal.azure.com>

2. Na barra de navegação esquerda, **selecione Azure Ative Diretório**

3. Selecione **Aplicações Empresariais,** em **seguida, todas as aplicações**.

4. Selecione **Adicionar uma aplicação**e selecione a categoria **All.**

5. Procure **SuccessFactors para Azure Ative Directory User Provisioning**, e adicione essa aplicação a partir da galeria.

6. Depois da aplicação ser adicionada e o ecrã de detalhes da aplicação ser mostrado, selecione **Provisioning**

7. Alterar o **modo** **de provisionamento** para **automático**

8. Complete a secção **de Credenciais de Administrador** da seguinte forma:

   * Nome de **utilizador da Administração** – Introduza o nome de utilizador da conta de utilizador da API SuccessFactors, com o ID da empresa anexado. Tem o formato: **username\@companyID**

   * **Senha de administração –** Introduza a palavra-passe da conta de utilizador da API SuccessFactors. 

   * **URL do inquilino –** Insira o nome dos serviços SuccessFactors OData API endpoint. Insira apenas o nome do host do servidor sem http ou HTTPS. Este valor deve parecer: **api-server-name.successfactors.com.**

   * **Email de notificação –** Insira o seu endereço de e-mail e verifique a caixa de verificação "enviar e-mail se ocorrer falha".
    > [!NOTE]
    > O Serviço de Provisionamento de AD Azure envia notificação por e-mail se o trabalho de provisionamento entrar em estado de [quarentena.](/azure/active-directory/manage-apps/application-provisioning-quarantine-status)

   * Clique no botão **de ligação** de teste. Se o teste de ligação for bem sucedido, clique no botão **Guardar** na parte superior. Se falhar, verifique se as credenciais e a URL do SuccessFactors são válidas.
    >[!div class="mx-imgBorder"]
    >![Portal do Azure](./media/sap-successfactors-inbound-provisioning/sf2aad-provisioning-creds.png)

   * Uma vez que as credenciais são guardadas com sucesso, a secção **De Mapeamentoapresentará** o mapeamento padrão **Synchronize SuccessFactors Users to Azure Ative Directory**

### <a name="part-2-configure-attribute-mappings"></a>Parte 2: configurar mapeamentos de atributo

Nesta seção, você configurará como os dados do usuário fluem do SuccessFactors para o Active Directory.

1. No separador provisionamento em **Mapeamentos,** clique em **Synchronize SuccessFactors Users to Azure Ative Directory**.

1. No campo **Source Object Scope,** pode selecionar quais os conjuntos de utilizadores em SuccessFactors que devem estar disponíveis para o fornecimento ao Azure AD, definindo um conjunto de filtros baseados em atributos. O escopo padrão é "todos os usuários em SuccessFactors". Filtros de exemplo:

   * Exemplo: escopo para usuários com personIdExternal entre 1 milhão e 2 milhões (exceto 2 milhões)

      * Atributo: personIdExternal

      * Operador: correspondência de REGEX

      * Valor: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Exemplo: somente funcionários e não usuários contingentes

      * Atributo: EmployeeID

      * Operador: não é nulo

   > [!TIP]
   > Quando você estiver configurando o aplicativo de provisionamento pela primeira vez, será necessário testar e verificar os mapeamentos e as expressões de atributo para garantir que ele esteja dando o resultado desejado. A Microsoft recomenda a utilização dos filtros de deteção no âmbito do Âmbito do **Objeto Fonte** para testar os seus mapeamentos com alguns utilizadores de teste do SuccessFactors. Depois de verificar se os mapeamentos funcionam, você pode remover o filtro ou expandi-lo gradualmente para incluir mais usuários.

   > [!CAUTION] 
   > O comportamento padrão do mecanismo de provisionamento é desabilitar/excluir usuários que saem do escopo. Isso pode não ser desejável em sua integração do SuccessFactors com o Azure AD. Para anular este comportamento padrão, consulte o artigo [Sem a eliminação das contas dos utilizadores que saem do âmbito](../app-provisioning/skip-out-of-scope-deletions.md)
  
1. No campo **Target Object Actions,** pode filtrar globalmente quais as ações que são realizadas no Diretório Ativo. **Criar** e **Atualizar** são mais comuns.

1. Na secção de **mapeamento sacar,** pode definir como os SuccessFactors individuais atribuem mapa aos atributos do Diretório Ativo.

  >[!NOTE]
  >Para a lista completa de Atributos De SucessoSuportados pela aplicação, consulte a Referência do [Atributo SuccessFactors](../app-provisioning/sap-successfactors-attribute-reference.md)


1. Clique num mapeamento de atributoexistente para atualizá-lo, ou clique **Em adicionar novo mapeamento** na parte inferior do ecrã para adicionar novos mapeamentos. Um mapeamento de atributo individual oferece suporte a essas propriedades:

      * **Tipo de mapeamento**

         * **Direto** – Escreve o valor dos SuccessFactors atribuiao atributo aD, sem alterações

         * **Constante** - Escreva um valor de corda estático e constante para o atributo AD

         * **Expressão** – Permite-lhe escrever um valor personalizado para o atributo AD, com base em um ou mais atributos SuccessFactors. [Para mais informações, consulte este artigo sobre expressões.](../app-provisioning/functions-for-customizing-application-data.md)

      * **Atributo de origem** - O atributo do utilizador dos SuccessFactors

      * **Valor predefinido** – Opcional. Se o atributo de origem tiver um valor vazio, o mapeamento gravará esse valor em vez disso.
            A configuração mais comum é deixar em branco.

      * **Atributo-alvo** – O atributo do utilizador no Diretório Ativo.

      * **Combine objetos usando este atributo** – Se este mapeamento deve ou não ser usado para identificar exclusivamente os utilizadores entre SuccessFactors e Ative Directy. Esse valor é normalmente definido no campo ID de trabalho para SuccessFactors, que normalmente é mapeado para um dos atributos de ID de funcionário em Active Directory.

      * **Precedência correspondente** – Podem ser definidos múltiplos atributos correspondentes. Quando há vários, eles são avaliados na ordem definida por esse campo. Assim que uma correspondência for encontrada, nenhum atributo correspondente será avaliado.

      * **Aplique este mapeamento**

         * **Sempre** – Aplique este mapeamento tanto na criação do utilizador como nas ações de atualização

         * **Apenas durante** a criação - Aplicar este mapeamento apenas em ações de criação de utilizadores

1. Para guardar os seus mapeamentos, clique em **Guardar** no topo da secção Attribute-Mapping.

Uma vez concluída a configuração de mapeamento do atributo, pode agora [ativar e lançar o serviço](#enable-and-launch-user-provisioning)de fornecimento do utilizador .

## <a name="enable-and-launch-user-provisioning"></a>Habilitar e iniciar o provisionamento de usuário

Depois que as configurações do aplicativo de provisionamento do SuccessFactors tiverem sido concluídas, você poderá ativar o serviço de provisionamento no portal do Azure.

> [!TIP]
> Por padrão, quando você ativa o serviço de provisionamento, ele inicia as operações de provisionamento para todos os usuários no escopo. Se houver erros no mapeamento ou nos problemas de dados do workday, o trabalho de provisionamento poderá falhar e entrar no estado de quarentena. Para evitar isto, como uma boa prática, recomendamos configurar o filtro **Source Object Scope** e testar os seus mapeamentos de atributos com alguns utilizadores de teste antes de lançar a sincronização completa para todos os utilizadores. Depois de verificar se os mapeamentos funcionam e estão fornecendo os resultados desejados, você pode remover o filtro ou expandi-lo gradualmente para incluir mais usuários.

1. No separador **provisionamento,** coloque o estado de **provisionamento** **ligado**.

2. Clique em **Guardar**.

3. Esta operação iniciará a sincronização inicial, que pode usar um número variável de horas dependendo de quantos usuários estiverem no locatário SuccessFactors. Você pode verificar a barra de progresso para acompanhar o progresso do ciclo de sincronização. 

4. A qualquer momento, verifique o separador de **registos de auditoria** no portal Azure para ver que ações o serviço de provisionamento realizou. Os logs de auditoria listam todos os eventos de sincronização individuais executados pelo serviço de provisionamento, como quais usuários estão sendo lidos do workday e, posteriormente, adicionados ou atualizados para Active Directory. 

5. Uma vez concluída a sincronização inicial, escreverá um relatório resumo de auditoria no separador **Provisioning,** como mostrado abaixo.

   > [!div class="mx-imgBorder"]
   > ![provisionamento de](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png) de progresso

## <a name="next-steps"></a>Passos seguintes

* [Saiba mais sobre os Atributos de Sucesso suportados para o fornecimento de entrada](../app-provisioning/sap-successfactors-attribute-reference.md)
* [Saiba como configurar a redação de e-mail para SuccessFactors](sap-successfactors-writeback-tutorial.md)
* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
* [Saiba como configurar um único sign-on entre SuccessFactors e Azure Ative Directory](successfactors-tutorial.md)
* [Saiba como integrar outras aplicações do SaaS com o Diretório Ativo Azure](tutorial-list.md)
* [Saiba como exportar e importar as suas configurações de provisionamento](../app-provisioning/export-import-provisioning-configuration.md)


