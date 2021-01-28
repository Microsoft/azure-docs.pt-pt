---
title: 'Tutorial: Configurar o provisionamento de entrada de workday em Azure Ative Directory | Microsoft Docs'
description: Saiba como configurar o provisionamento de entrada de Workday a Azure AD
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: tutorial
ms.workload: identity
ms.date: 05/26/2020
ms.author: chmutali
ms.openlocfilehash: ef4381f305292b366348aa3729209dc3f5e8c87b
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954094"
---
# <a name="tutorial-configure-workday-to-azure-ad-user-provisioning"></a>Tutorial: Configurar o Workday para o provisionamento do utilizador Azure AD
O objetivo deste tutorial é mostrar os passos necessários para a disponibilização de dados dos trabalhadores do Workday para o Azure Ative Directory. 

>[!NOTE]
>Utilize este tutorial se os utilizadores que pretende prestar a partir do Workday são utilizadores apenas na nuvem que não precisam de uma conta AD no local. Se os utilizadores necessitarem apenas de conta AD no local ou da conta AD e AD Azure, consulte o tutorial no dia de trabalho de configuração para o provisionamento do utilizador [do Ative Directory.](workday-inbound-tutorial.md) 

## <a name="overview"></a>Descrição Geral

O [serviço de prestação de serviços de atendimento ao utilizador Azure Ative Directory](../app-provisioning/user-provisioning.md) integra-se com a API de Recursos Humanos do [Workday,](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) a fim de prestar contas de utilizadores. O serviço de fornecimento de utilizadores do Workday suportado pelo serviço de fornecimento de utilizadores Azure AD permite automatizar os seguintes recursos humanos e cenários de gestão do ciclo de vida da identidade:

* **Contratação de novos colaboradores** - Quando um novo empregado é adicionado ao Workday, uma conta de utilizador é criada automaticamente no Azure Ative Directory e opcionalmente microsoft 365 e [outras aplicações SaaS suportadas pela Azure AD,](../app-provisioning/user-provisioning.md)com a desativação do endereço de e-mail para Workday.

* **Atribuição de colaboradores e atualizações de perfis** - Quando um registo de empregados é atualizado no Workday (como o seu nome, título ou gestor), a sua conta de utilizador será automaticamente atualizada Azure Ative Directory e opcionalmente Microsoft 365 e [outras aplicações SaaS suportadas pela Azure AD](../app-provisioning/user-provisioning.md).

* **Rescisões de funcionários** - Quando um empregado é encerrado no Workday, a sua conta de utilizador é automaticamente desativada no Azure Ative Directory e opcionalmente microsoft 365 e [outras aplicações SaaS suportadas pela Azure AD](../app-provisioning/user-provisioning.md).

* **Recontrações de empregados** - Quando um empregado é recontratado no Workday, a sua conta antiga pode ser automaticamente reativada ou re provisionada (dependendo da sua preferência) para o Azure Ative Directory e opcionalmente microsoft 365 e [outras aplicações SaaS apoiadas pela Azure AD](../app-provisioning/user-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Para quem é a solução de provisionamento do utilizador mais adequada?

Esta solução de acumento do utilizador do Azure Ative Directory é ideal para:

* Organizações que desejam uma solução pré-construída e baseada na nuvem para o fornecimento de utilizadores do Workday

* Organizações que exigem o fornecimento direto de utilizadores do Workday para o Azure Ative Directory

* Organizações que exigem que os utilizadores sejam a provisionados usando dados obtidos a partir do Dia do Trabalho

* Organizações que usam o Microsoft 365 para e-mail

## <a name="solution-architecture"></a>Arquitetura de soluções

Esta secção descreve a arquitetura de solução de atenção de utilizadores de ponta a ponta para utilizadores apenas na nuvem. Existem dois fluxos relacionados:

* **Fluxo de dados de RH autoritários – do Workday ao Azure Ative Directory:** Neste evento de trabalhadores de fluxo (como Novas Contratações, Transferências, Rescisões) ocorrem primeiro no Workday e, em seguida, os dados do evento fluem para o Diretório Ativo Azure. Dependendo do evento, pode levar a operações de criação/atualização/ativação/desativação em Azure AD.
* **Fluxo de writeback – do Diretório Ativo para o Workday:** Uma vez que a criação da conta esteja completa no Ative Directory, é sincronizado com Azure AD através do Azure AD Connect e informações como e-mail, nome de utilizador e número de telefone podem ser escritas de volta para o Workday.

  ![Descrição Geral](./media/workday-inbound-tutorial/workday-cloud-only-provisioning.png)

### <a name="end-to-end-user-data-flow"></a>Fluxo de dados de utilizador de ponta a ponta

1. A equipa de RH realiza transações de trabalhadores (Joiners/Movers/Leavers ou New Hires/Transfers/Terminations) no Workday Employee Central
2. O Serviço de Provisionamento Azure AD executa sincronizações programadas de identidades da Workday EC e identifica alterações que precisam de ser processadas para sincronização com o Diretório Ativo no local.
3. O Serviço de Provisionamento AZure AD determina a alteração e invoca a criação/atualização/ativação/desativação/desativação para o utilizador em Azure AD.
4. Se a aplicação [Workday Writeback](workday-writeback-tutorial.md) estiver configurada, obtém atributos como e-mail, nome de utilizador e número de telefone da Azure AD. 
5. O serviço de fornecimento de Azure AD define e-mail, nome de utilizador e número de telefone no Workday.

## <a name="planning-your-deployment"></a>Planejando a sua implantação

Configurar o fornecimento de utilizadores orientados pela Cloud HR de Workday a Azure AD requer um planeamento considerável cobrindo diferentes aspetos, tais como:

* Determinação do ID correspondente 
* Mapeamento do atributo
* Transformação de atributos 
* Filtros de âmbito

Consulte o [plano de implementação de RH em nuvem](../app-provisioning/plan-cloud-hr-provision.md) para obter orientações abrangentes em torno destes tópicos. 

## <a name="configure-integration-system-user-in-workday"></a>Configure o utilizador do sistema de integração no Workday

Consulte o utilizador do [sistema de integração configurar](workday-inbound-tutorial.md#configure-integration-system-user-in-workday) a secção para criar uma conta de utilizador do sistema de integração Workday com permissões para recuperar dados dos trabalhadores. 

## <a name="configure-user-provisioning-from-workday-to-azure-ad"></a>Configure o fornecimento de utilizadores de Workday a Azure AD

As secções seguintes descrevem passos para configurar o fornecimento do utilizador de Workday a Azure AD para implementações apenas na nuvem.

* [Adicionar a app de conector Azure AD e criar a ligação ao Workday](#part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday)
* [Configurar mapeamentos de atributos Workday e Azure AD](#part-2-configure-workday-and-azure-ad-attribute-mappings)
* [Permitir e lançar o fornecimento de utilizadores](#enable-and-launch-user-provisioning)

### <a name="part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Parte 1: Adicionar a app de ligação azure AD e criar a ligação ao Workday

**Para configurar o Workday para o Azure Ative Directory provisioning para utilizadores apenas na nuvem:**

1. Aceda a <https://portal.azure.com>.

2. No portal Azure, procure e selecione **O Diretório Ativo Azure**.

3. Selecione **Aplicações empresariais,** em seguida, **todas as aplicações**.

4. **Selecione Adicione uma aplicação** e, em seguida, selecione a categoria **'Todos'.**

5. Procure o trabalho para **o azure AD, e** adicione a aplicação da galeria.

6. Depois de adicionar a aplicação e mostrar o ecrã de detalhes da aplicação, selecione **Provisioning**.

7. Altere o **modo** **de provisionamento** para **Automático**.

8. Preencha a secção **credenciais de administração** da seguinte forma:

   * **Nome de utilizador do dia útil** – Introduza o nome de utilizador da conta do sistema de integração workday, com o nome de domínio do inquilino anexado. Deve parecer algo como: username@contoso4

   * **Senha do dia de trabalho -** Introduza a palavra-passe da conta do sistema de integração workday

   * **URL API de Serviços Web workday –** Introduza o URL no ponto final dos serviços web workday para o seu inquilino. O URL determina a versão da API dos Serviços Web workday utilizada pelo conector. 
   
     | Formato do URL | Versão API da WWS usada | Alterações XPATH necessárias |
     |------------|----------------------|------------------------|
     | https://####.workday.com/ccx/service/tenantName | v21.1 | No |
     | https://####.workday.com/ccx/service/tenantName/Human_Resources | v21.1 | No |
     | https://####.workday.com/ccx/service/tenantName/Human_Resources/v##.# | v## . # | Yes |

      > [!NOTE]
     > Se nenhuma informação de versão for especificada no URL, a aplicação utiliza o Workday Web Services (WWS) v21.1 e não são necessárias alterações nas expressões API padrão enviadas com a aplicação. Para utilizar uma versão API da WWS específica, especifique o número da versão no URL <br>
     > Exemplo: `https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources/v34.0` <br>
     > <br> Se estiver a utilizar uma lista de atributos de Edição de API wws v30.0+, antes de ligar o trabalho de provisionamento, por favor atualize as **expressões API XPATH** em **"O Mapeamento de Atributos > Opções Avançadas -> Editar lista de atributos para Workday** referente à secção Gerir a [sua configuração](workday-inbound-tutorial.md#managing-your-configuration) e [referência de atributos workday](../app-provisioning/workday-attribute-reference.md#xpath-values-for-workday-web-services-wws-api-v30).  

   * **E-mail de notificação –** Insira o seu endereço de e-mail e verifique a caixa de verificação "enviar e-mail se ocorrer falha".

   * Clique no botão **De Ligação de Teste.**

   * Se o teste de ligação for bem sucedido, clique no botão **Guardar** na parte superior. Se falhar, verifique duas vezes se o URL do dia de trabalho e as credenciais são válidas no Workday.

### <a name="part-2-configure-workday-and-azure-ad-attribute-mappings"></a>Parte 2: Configurar os mapeamentos de atributos Workday e Azure AD

Nesta secção, irá configurar como os dados dos utilizadores fluem de Workday para Azure Ative Directory para utilizadores apenas na nuvem.

1. No separador Provisioning em **Mappings,** clique em **Sincronizar Trabalhadores para Azure AD**.

2. No campo Âmbito do **Objeto Fonte,** pode selecionar quais os conjuntos de utilizadores no Workday que devem estar em possibilidade de provisão para Azure AD, definindo um conjunto de filtros baseados em atributos. O âmbito padrão é "todos os utilizadores no Workday". Filtros de exemplo:

   * Exemplo: Âmbito para utilizadores com IDs do Trabalhador entre 1000000 e 2000000

      * Atributo: WorkerID

      * Operador: REGEX Match

      * Valor: (1[0-9][0-9][0-9][0-9][0-9][0-9][0-9])

   * Exemplo: Apenas trabalhadores contingentes e não empregados regulares

      * Atributo: ContingentID

      * Operador: NÃO É NULO

3. No campo **Target Object Actions,** pode filtrar globalmente as ações que são executadas no Azure AD. **Criar**  e **Atualizar** são mais comuns.

4. Na secção **de mapeamentos do Atributo,** pode definir como o mapa individual do Dia de Trabalho atribui o mapa aos atributos do Ative Directory.

5. Clique num mapeamento de atributos existente para atualizá-lo ou clique em **Adicionar novo mapeamento** na parte inferior do ecrã para adicionar novos mapeamentos. Um mapeamento de atributos individuais suporta estas propriedades:

   * **Tipo de Mapeamento**

      * **Direto** – Escreve o valor do atributo Workday ao atributo AD, sem alterações

      * **Constante** - Escreva um valor estático e constante de cordas para o atributo AD

      * **Expressão** – Permite-lhe escrever um valor personalizado ao atributo AD, com base num ou mais atributos do Dia do Trabalho. [Para mais informações, consulte este artigo sobre expressões.](../app-provisioning/functions-for-customizing-application-data.md)

   * **Atributo de origem** - O atributo do utilizador do Workday. Se o atributo que procura não estiver presente, consulte [personalizar a lista de atributos do utilizador do Workday](workday-inbound-tutorial.md#customizing-the-list-of-workday-user-attributes).

   * **Valor predefinido** – Opcional. Se o atributo de origem tiver um valor vazio, o mapeamento escreverá este valor.
            A configuração mais comum é deixar este em branco.

   * **Atributo-alvo** – O atributo do utilizador em Azure AD.

   * **Combine objetos utilizando este atributo** – Se este atributo deve ou não ser usado para identificar exclusivamente os utilizadores entre o Workday e o AD Azure. Este valor é tipicamente definido no campo de ID do Trabalhador para o Dia de Trabalho, que é tipicamente mapeado para o atributo de ID do empregado (novo) ou um atributo de extensão em Azure AD.

   * **Precedência correspondente** - Podem ser definidos vários atributos correspondentes. Quando há múltiplos, são avaliados na ordem definida por este campo. Assim que um fósforo é encontrado, não são avaliados mais atributos correspondentes.

   * **Aplique este mapeamento**

     * **Always** – Aplicar este mapeamento tanto na criação como nas ações de atualização do utilizador

     * **Apenas durante a criação** - Aplique este mapeamento apenas em ações de criação de utilizadores

6. Para guardar os seus mapeamentos, clique em **Guardar** na parte superior da secção Attribute-Mapping.


## <a name="enable-and-launch-user-provisioning"></a>Permitir e lançar o fornecimento de utilizadores

Uma vez concluídas as configurações da aplicação de provisionamento workday, pode ligar o serviço de prestação no portal Azure.

> [!TIP]
> Por padrão, quando ligar o serviço de fornecimento, iniciará operações de provisionamento para todos os utilizadores no âmbito. Se houver erros no mapeamento ou problemas de dados do Dia de Trabalho, então o trabalho de provisionamento pode falhar e entrar no estado de quarentena. Para evitar isto, como uma boa prática, recomendamos configurar o filtro **Source Object Scope** e testar os mapeamentos do seu atributo com alguns utilizadores de teste antes de lançar a sincronização completa para todos os utilizadores. Uma vez verificado que os mapeamentos funcionam e lhe estão a dar os resultados desejados, então pode remover o filtro ou expandi-lo gradualmente para incluir mais utilizadores.

1. No **separador Provisioning,** desa fixação do **Estado de Provisionamento** para **On**.

2. Clique em **Guardar**.

3. Esta operação iniciará a sincronização inicial, que pode demorar um número variável de horas dependendo de quantos utilizadores estão no arrendatário do Workday. Pode verificar a barra de progresso para acompanhar o progresso do ciclo de sincronização. 

4. A qualquer momento, consulte o **separador de registos** de auditoria no portal Azure para ver que ações o serviço de prestação de serviços executou. Os registos de auditoria listam todos os eventos de sincronização individuais realizados pelo serviço de fornecimento, tais como os utilizadores que estão a ser lidos fora do Workday e posteriormente adicionados ou atualizados ao Azure Ative Directory. 

5. Uma vez concluída a sincronização inicial, escreverá um relatório de resumo de auditoria no **separador Provisioning,** como mostrado abaixo.

   > [!div class="mx-imgBorder"]
   > ![Provisão de barras de progresso](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>Passos seguintes

* [Saiba mais sobre os cenários de integração do AZure AD e do Workday e as chamadas de serviço web](../app-provisioning/workday-integration-reference.md)
* [Saiba mais sobre atributos de trabalho suportados para o provisionamento de entrada](../app-provisioning/workday-attribute-reference.md)
* [Saiba como configurar o Writeback workday](workday-writeback-tutorial.md)
* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)
* [Saiba como configurar um único sign-on entre workday e Azure Ative Directory](workday-tutorial.md)
* [Saiba como exportar e importar as suas configurações de provisionamento](../app-provisioning/export-import-provisioning-configuration.md)


