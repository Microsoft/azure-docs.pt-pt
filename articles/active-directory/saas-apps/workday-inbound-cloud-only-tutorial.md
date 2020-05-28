---
title: 'Tutorial: Configurar o fornecimento de entrada no Diretório Ativo do Azure Microsoft Docs'
description: Saiba como configurar o fornecimento de entrada do Dia de Trabalho para o Azure AD
services: active-directory
author: cmmdesai
documentationcenter: na
manager: daveba
ms.assetid: fac4f61e-d942-4429-a297-9ba74db95077
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/26/2020
ms.author: chmutali
ms.openlocfilehash: 6fb80af84379a1a0bc174a7318c8150a98bea95e
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84041814"
---
# <a name="tutorial-configure-workday-to-azure-ad-user-provisioning"></a>Tutorial: Configure workday to Azure AD user provisioning
O objetivo deste tutorial é mostrar os passos necessários para fornecer dados dos trabalhadores desde o Workday até ao Diretório Ativo Azure. 

>[!NOTE]
>Utilize este tutorial se os utilizadores que pretende fornecer a partir do Workday forem utilizadores apenas na nuvem que não necessitem de uma conta AD no local. Se os utilizadores necessitarem apenas de uma conta AD no local ou da conta AD e Azure AD, consulte o tutorial no [workday configurado para](workday-inbound-tutorial.md) o fornecimento de utilizadores de Diretório Ativo. 

## <a name="overview"></a>Descrição geral

O serviço de prestação de [utilizadores do Azure Ative Directory](../app-provisioning/user-provisioning.md) integra-se com a API de Recursos Humanos do Dia do [Trabalho,](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) a fim de fornecer contas de utilizador. Os fluxos de trabalho de fornecimento de utilizadores do Workday suportados pelo serviço de prestação de utilizadores da AD Azure permitem a automatização dos seguintes recursos humanos e cenários de gestão do ciclo de vida identitário:

* **Contratação** de novos colaboradores - Quando um novo colaborador é adicionado ao Workday, uma conta de utilizador é automaticamente criada no Diretório Ativo Azure e opcionalmente office 365 e [outras aplicações SaaS suportadas pela Azure AD,](../app-provisioning/user-provisioning.md)com redação do endereço de e-mail para o Workday.

* **Atributo sipróprio e atualizações** de perfil - Quando um registo de empregados é atualizado no Workday (como o seu nome, título ou gestor), a sua conta de utilizador será automaticamente atualizada do Azure Ative Directory e opcionalmente office 365 e [outras aplicações SaaS suportadas pela Azure AD](../app-provisioning/user-provisioning.md).

* **Rescisões** de funcionários - Quando um trabalhador é despedido no Dia do Trabalho, a sua conta de utilizador é automaticamente desativada no Diretório Ativo Azure e opcionalmente office 365 e [outras aplicações SaaS suportadas pela Azure AD](../app-provisioning/user-provisioning.md).

* **Recontratações** de empregados - Quando um empregado é recontratado no Workday, a sua conta antiga pode ser automaticamente reativada ou represtada (dependendo da sua preferência) ao Diretório Ativo Azure e opcionalmente office 365 e [outras aplicações SaaS suportadas pela Azure AD](../app-provisioning/user-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Para quem é esta solução de fornecimento de utilizadores mais adequada?

Esta solução de fornecimento de utilizadores do Workday para azure Ative Directory é ideal para:

* Organizações que desejem uma solução pré-construída e baseada na nuvem para o fornecimento de utilizadores do Dia de Trabalho

* Organizações que exigem o fornecimento direto de utilizadores do Workday para o Diretório Ativo Azure

* Organizações que exigem que os utilizadores sejam aprovisionados utilizando dados obtidos a partir do Dia de Trabalho

* Organizações que usam o Office 365 para e-mail

## <a name="solution-architecture"></a>Arquitetura de soluções

Esta secção descreve a arquitetura de solução de solução de fornecimento de utilizadores de ponta a ponta para utilizadores apenas na nuvem. Existem dois fluxos relacionados:

* Fluxo de dados de **RH autoritário – do Dia de Trabalho ao Diretório Ativo Azure:** Neste fluxo os eventos dos trabalhadores (tais como Novas Contratações, Transferências, Rescisões) ocorrem primeiro no Dia de Trabalho e, em seguida, os dados do evento fluem para o Diretório Ativo Azure. Dependendo do evento, pode levar a operações de criação/atualização/ativação/desativação em Azure AD.
* **Fluxo de reprodução – do Diretório Ativo no local para o Dia do Trabalho:** Uma vez que a criação da conta esteja completa no Ative Directory, é sincronizada com o Azure AD Connect através do Azure AD Connect e informações como e-mail, nome de utilizador e número de telefone podem ser redigidas no Workday.

  ![Descrição geral](./media/workday-inbound-tutorial/workday-cloud-only-provisioning.png)

### <a name="end-to-end-user-data-flow"></a>Fluxo de dados de utilizador de ponta a ponta

1. A equipa de RH realiza transações de trabalhadores (Joiners/Movers/Leavers ou New Hires/Transfers/Terminations) na Workday Employee Central
2. O Serviço de Provisionamento de AD Azure executa sincronizações programadas de identidades da Workday EC e identifica alterações que precisam de ser processadas para sincronização com o Diretório Ativo no local.
3. O Serviço de Provisionamento de AD Azure determina a alteração e invoca a criação/atualização/ativação/desativação do funcionamento do utilizador em Azure AD.
4. Se a aplicação [Workday Writeback](workday-writeback-tutorial.md) estiver configurada, obtém atributos como e-mail, nome de utilizador e número de telefone da Azure AD. 
5. O serviço de provisionamento de AD Azure define o e-mail, o nome de utilizador e o número de telefone no Workday.

## <a name="planning-your-deployment"></a>Planejando a sua implantação

Configurar o fornecimento de utilizadores conduzidos pela Cloud HR do Workday para o Azure AD requer um planeamento considerável que abranja diferentes aspetos, tais como:

* Determinação do ID correspondente 
* Mapeamento do atributo
* Transformação do atributo 
* Filtros de âmbito

Consulte o plano de [implantação](../app-provisioning/plan-cloud-hr-provision.md) de RH em nuvem para diretrizes abrangentes em torno destes tópicos. 

## <a name="configure-integration-system-user-in-workday"></a>Configure o utilizador do sistema de integração no Dia do Trabalho

Consulte a secção configurar o utilizador do sistema de [integração](workday-inbound-tutorial.md#configure-integration-system-user-in-workday) para criar uma conta de utilizador do sistema de integração Workday com permissões para recuperar dados dos trabalhadores. 

## <a name="configure-user-provisioning-from-workday-to-azure-ad"></a>Configure o fornecimento de utilizadores do Dia de Trabalho para o Azure AD

As seguintes secções descrevem passos para configurar o fornecimento de utilizadores do Workday para o Azure AD para implementações apenas na nuvem.

* [Adicionar a aplicação de conector Azure AD e criar a ligação ao Dia do Trabalho](#part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday)
* [Configure Workday e Azure AD atribuem mapeamentos](#part-2-configure-workday-and-azure-ad-attribute-mappings)
* [Ativar e lançar o fornecimento de utilizadores](#enable-and-launch-user-provisioning)

### <a name="part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Parte 1: Adicionar a aplicação de conector Azure AD e criar a ligação ao Dia do Trabalho

**Para configurar o fornecimento de Workday para Azure Ative Directory para utilizadores apenas na nuvem:**

1. Aceda a <https://portal.azure.com>.

2. No portal Azure, procure e selecione **Azure Ative Directory**.

3. Selecione **Aplicações Empresariais,** em **seguida, todas as aplicações**.

4. Selecione **Adicionar uma aplicação**e, em seguida, selecione a categoria **All.**

5. Procure o Workday para o fornecimento de **utilizadores da AD Azure**e adicione essa aplicação a partir da galeria.

6. Depois da adição da aplicação e do ecrã de detalhes da aplicação, selecione **Provisioning**.

7. Mude o **modo** **de provisionamento** para **automático**.

8. Complete a secção **de Credenciais de Administrador** da seguinte forma:

   * Nome de **utilizador workday** – Introduza o nome de utilizador da conta do sistema de integração Workday, com o nome de domínio do arrendatário anexado. Deve parecer algo como:username@contoso4

   * **Senha do dia de trabalho -** Insira a palavra-passe da conta do sistema de integração do Dia de Trabalho

   * **Url da API de Serviços Web de Workday –** Insira o URL no ponto final dos serviços web workday para o seu inquilino. O URL determina a versão da API dos Serviços Web workday utilizada pelo conector. 
   
     | Formato do URL | Versão WWS API utilizada | Alterações XPATH necessárias |
     |------------|----------------------|------------------------|
     | https://####.workday.com/ccx/service/tenantName | v21.1 | Não |
     | https://####.workday.com/ccx/service/tenantName/Human_Resources | v21.1 | Não |
     | https://####.workday.com/ccx/service/tenantName/Human_Resources/v##.# | v##. # | Sim |

      > [!NOTE]
     > Se nenhuma informação de versão for especificada no URL, a aplicação utiliza serviços web de workday (WWS) v21.1 e não são necessárias alterações às expressões padrão da API XPATH enviadas com a app. Para utilizar uma versão específica da WWS API, especifique o número da versão no URL <br>
     > Exemplo: `https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources/v34.0` <br>
     > <br> Se estiver a utilizar uma wws API v30.0+, antes de ligar o trabalho de provisionamento, por favor atualize as **expressões XPATH API** em termos de **Attribute Mapping -> Advanced Options -> Editar lista de atributos para o Dia** de Trabalho, referindo-se à secção [Gestão da sua configuração](workday-inbound-tutorial.md#managing-your-configuration) e referência de [atributo de trabalho](../app-provisioning/workday-attribute-reference.md#xpath-values-for-workday-web-services-wws-api-v30).  

   * **Email de notificação –** Insira o seu endereço de e-mail e verifique a caixa de verificação "enviar e-mail se ocorrer falha".

   * Clique no botão **de ligação** de teste.

   * Se o teste de ligação for bem sucedido, clique no botão **Guardar** na parte superior. Se falhar, verifique duas vezes se o URL do Dia de Trabalho e as credenciais são válidas no Dia do Trabalho.

### <a name="part-2-configure-workday-and-azure-ad-attribute-mappings"></a>Parte 2: Configurar mapeamentos de atributos do Dia de Trabalho e do Azure AD

Nesta secção, irá configurar como os dados dos utilizadores fluem do Workday para o Azure Ative Directory para utilizadores apenas na nuvem.

1. No separador provisionamento em **Mapeamentos,** clique em **Sincronizar trabalhadores para Azure AD**.

2. No campo **Source Object Scope,** pode selecionar quais os conjuntos de utilizadores no Workday que devem estar disponíveis para o fornecimento ao Azure AD, definindo um conjunto de filtros baseados em atributos. O âmbito padrão é "todos os utilizadores no Dia do Trabalho". Filtros de exemplo:

   * Exemplo: Margem de manobra para utilizadores com IDs de trabalhador entre 10000000 e 2000000

      * Atributo: WorkerID

      * Operador: Regex Match

      * Valor: (1[0-9][0-9][0-9][0-9][0-9][0-9]]]]

   * Exemplo: Apenas trabalhadores contingentes e não trabalhadores regulares

      * Atributo: ContingentID

      * Operador: NÃO É NULO

3. No campo **Target Object Actions,** pode filtrar globalmente quais as ações que são realizadas em Azure AD. **Criar** e **Atualizar** são mais comuns.

4. Na secção de **mapeamento sacar,** pode definir como cada dia de trabalho atribui o mapa aos atributos do Diretório Ativo.

5. Clique num mapeamento de atributoexistente para atualizá-lo, ou clique **Em adicionar novo mapeamento** na parte inferior do ecrã para adicionar novos mapeamentos. Um mapeamento individual de atributos suporta estas propriedades:

   * **Tipo de mapeamento**

      * **Direto** – Escreve o valor do atributo do Dia de Trabalho ao atributo AD, sem alterações

      * **Constante** - Escreva um valor de corda estático e constante para o atributo AD

      * **Expressão** – Permite-lhe escrever um valor personalizado para o atributo AD, com base num ou mais atributos do Dia de Trabalho. [Para mais informações, consulte este artigo sobre expressões.](../app-provisioning/functions-for-customizing-application-data.md)

   * **Atributo de origem** - O atributo do utilizador do Dia de Trabalho. Se o atributo que procura não estiver presente, consulte personalizar [a lista de atributos do utilizador do Dia](workday-inbound-tutorial.md#customizing-the-list-of-workday-user-attributes)de Trabalho .

   * **Valor predefinido** – Opcional. Se o atributo de origem tiver um valor vazio, o mapeamento escreverá este valor.
            A configuração mais comum é deixar isto em branco.

   * **Atributo-alvo** – O atributo do utilizador em Azure AD.

   * **Combine objetos usando este atributo** – Se este atributo deve ou não ser usado para identificar exclusivamente os utilizadores entre o Workday e o Azure AD. Este valor é tipicamente definido no campo de ID do trabalhador para o Dia de Trabalho, que é tipicamente mapeado para o atributo de ID do empregado (novo) ou um atributo de extensão em Azure AD.

   * **Precedência correspondente** – Podem ser definidos múltiplos atributos correspondentes. Quando existem múltiplos, são avaliados na ordem definida por este campo. Assim que um jogo é encontrado, não são avaliados mais atributos correspondentes.

   * **Aplique este mapeamento**

     * **Sempre** – Aplique este mapeamento tanto na criação do utilizador como nas ações de atualização

     * **Apenas durante** a criação - Aplicar este mapeamento apenas em ações de criação de utilizadores

6. Para guardar os seus mapeamentos, clique em **Guardar** no topo da secção Attribute-Mapping.


## <a name="enable-and-launch-user-provisioning"></a>Ativar e lançar o fornecimento de utilizadores

Uma vez concluídas as configurações da aplicação de provisionamento Workday, pode ligar o serviço de provisionamento no portal Azure.

> [!TIP]
> Por predefinição, quando ligar o serviço de provisionamento, iniciará operações de provisionamento para todos os utilizadores no âmbito. Se houver erros nas questões de mapeamento ou dados do Dia do Trabalho, então o trabalho de provisionamento pode falhar e ir para o estado de quarentena. Para evitar isto, como uma boa prática, recomendamos configurar o filtro **Source Object Scope** e testar os seus mapeamentos de atributos com alguns utilizadores de teste antes de lançar a sincronização completa para todos os utilizadores. Uma vez verificado que os mapeamentos funcionam e estão a dar-lhe os resultados desejados, então pode remover o filtro ou expandi-lo gradualmente para incluir mais utilizadores.

1. No separador **provisionamento,** coloque o estado de **provisionamento** **ligado**.

2. Clique em **Guardar**.

3. Esta operação iniciará a sincronização inicial, que pode demorar um número variável de horas dependendo de quantos utilizadores estão no inquilino do Dia do Trabalho. Pode verificar a barra de progresso para acompanhar o progresso do ciclo de sincronização. 

4. A qualquer momento, verifique o separador de **registos de auditoria** no portal Azure para ver que ações o serviço de provisionamento realizou. Os registos de auditoria listam todos os eventos de sincronização individuais realizados pelo serviço de provisionamento, tais como os utilizadores que estão a ser lidos fora do Workday e, posteriormente, adicionados ou atualizados para o Diretório Ativo do Azure. 

5. Uma vez concluída a sincronização inicial, escreverá um relatório resumo de auditoria no separador **Provisioning,** como mostrado abaixo.

   > [!div class="mx-imgBorder"]
   > ![Fornecimento de barra de progresso](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>Próximos passos

* [Saiba mais sobre atributos de trabalho suportados para o fornecimento de entrada](../app-provisioning/workday-attribute-reference.md)
* [Saiba como configurar workday Writeback](workday-writeback-tutorial.md)
* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
* [Saiba como configurar um único sign-on entre o Workday e o Azure Ative Directory](workday-tutorial.md)
* [Saiba como integrar outras aplicações do SaaS com o Diretório Ativo Azure](tutorial-list.md)
* [Saiba como exportar e importar as suas configurações de provisionamento](../app-provisioning/export-import-provisioning-configuration.md)


