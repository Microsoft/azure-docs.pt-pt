---
title: 'Tutorial: Configurar a gravação do SAP SuccessFactors no Azure Ative Directory Microsoft Docs'
description: Saiba como configurar o atributo de volta aos SAP SuccessFactors da Azure AD
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: tutorial
ms.workload: identity
ms.date: 10/14/2020
ms.author: chmutali
ms.openlocfilehash: d39e00a80ab167936a749c73867b4343e6ed9d76
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006443"
---
# <a name="tutorial-configure-attribute-write-back-from-azure-ad-to-sap-successfactors"></a>Tutorial: Configure atribui write-back de Azure AD a SAP SuccessFactors
O objetivo deste tutorial é mostrar os passos para anular atributos de Azure AD a SAP SuccessFactors Employee Central. 

## <a name="overview"></a>Descrição geral

Pode configurar a app SAP SuccessFactors Writeback para escrever atributos específicos do Azure Ative Directory para a SAP SuccessFactors Employee Central. A aplicação de atribuição de recíprocos SuccessFactors suporta a atribuição de valores aos seguintes atributos Central dos Colaboradores:

* Email de Trabalho
* Nome de utilizador
* Número de telefone de negócios (incluindo código de país, código de área, número e extensão)
* Bandeira primária do número de telefone do negócio
* Número de telemóvel (incluindo código de país, código de área, número)
* Bandeira primária do telemóvel 
* Atributos personalizados do utilizador 01-custom15
* atributo loginMethod

> [!NOTE]
> Esta aplicação não tem qualquer dependência dos SuccessFactors que inserem aplicações de integração de utilizadores. Pode configugá-lo independente dos [SuccessFactors para](sap-successfactors-inbound-provisioning-tutorial.md) app de provisionamento de AD no local ou para a aplicação de provisionamento [Azure AD.](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md)

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Para quem é a solução de provisionamento do utilizador mais adequada?

Esta solução de provisionamento do utilizador SuccessFactors Writeback é ideal para:

* Organizações que usam o Microsoft 365 que desejam rescrever atributos autoritários geridos por TI (como endereço de e-mail, telefone, nome de utilizador) de volta à SuccessFactors Employee Central.

## <a name="configuring-successfactors-for-the-integration"></a>Configurar o SucessoFactors para a integração

Todos os fatores de sucesso que aprovisionam conectores requerem credenciais de uma conta SuccessFactors com as permissões certas para invocar as APIs de OData Central do Colaborador. Esta secção descreve passos para criar a conta de serviço em SuccessFactors e conceder permissões apropriadas. 

* [Criar/identificar conta de utilizador da API em SuccessFactors](#createidentify-api-user-account-in-successfactors)
* [Criar um papel de permissões da API](#create-an-api-permissions-role)
* [Criar um Grupo de Permissão para o utilizador API](#create-a-permission-group-for-the-api-user)
* [Atribuir papel de permissão ao Grupo de Permissão](#grant-permission-role-to-the-permission-group)

### <a name="createidentify-api-user-account-in-successfactors"></a>Criar/identificar conta de utilizador da API em SuccessFactors
Trabalhe com a sua equipa de administradores successFactors ou parceiro de implementação para criar ou identificar uma conta de utilizador em SuccessFactors que será usada para invocar as APIs OData. O nome de utilizador e as credenciais de senha desta conta serão necessárias ao configurar as aplicações de provisionamento em Azure AD. 

### <a name="create-an-api-permissions-role"></a>Criar um papel de permissões da API

1. Faça login no SAP SuccessFactors com uma conta de utilizador que tenha acesso ao Centro de Administração.
1. Procure por *Gerir funções de permissões* e, em seguida, **selecione 'Gerir funções'** a partir dos resultados da pesquisa.

   ![Gerir funções de permissões](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)

1. A partir da Lista de Funções de Permissão, clique em **Criar Novo**.

   > [!div class="mx-imgBorder"]
   > ![Criar nova função de permissão](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png)

1. Adicione um nome de **papel** e **descrição** para o novo papel de permissão. O nome e a descrição devem indicar que a função é para permissões de utilização da API.

   > [!div class="mx-imgBorder"]
   > ![Detalhe de função de permissão](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)

1. Nas definições de Permissão, clique em **Permissão...** e, em seguida, desloque a lista de permissões e clique em **Gerir Ferramentas de Integração**. Verifique a caixa para **permitir o acesso do Administrador à API OData através da Autenticação Básica.**

   > [!div class="mx-imgBorder"]
   > ![Gerir ferramentas de integração](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)

1. Desloque-se na mesma caixa e selecione **API Central do Empregado**. Adicione permissões como mostrado abaixo para ler usando a API ODATA e edite usando a API ODATA. Selecione a opção de edição se pretender utilizar a mesma conta para o cenário de "SuccessFactors". 

   > [!div class="mx-imgBorder"]
   > ![Ler permissões de escrita](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)

1. Clique em **'Feito'.** Clique em **Guardar Alterações**.

### <a name="create-a-permission-group-for-the-api-user"></a>Criar um Grupo de Permissão para o utilizador API

1. No Centro de Administração SuccessFactors, procure *grupos de permissões de gestão* e, em seguida, selecione **'Gerir grupos de permissão'** a partir dos resultados da pesquisa.

   > [!div class="mx-imgBorder"]
   > ![Gerir grupos de permissões](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)

1. A partir da janela 'Gerir grupos' de permissão, clique em **Criar Novo**.

   > [!div class="mx-imgBorder"]
   > ![Adicionar novo grupo](./media/sap-successfactors-inbound-provisioning/create-new-group.png)

1. Adicione um nome de grupo para o novo grupo. O nome do grupo deve indicar que o grupo é para utilizadores de API.

   > [!div class="mx-imgBorder"]
   > ![Nome do grupo de permissão](./media/sap-successfactors-inbound-provisioning/permission-group-name.png)

1. Adicione membros ao grupo. Por exemplo, pode selecionar o nome de **utilizador** do menu de entrega do People Pool e, em seguida, introduzir o nome de utilizador da conta API que será utilizada para a integração. 

   > [!div class="mx-imgBorder"]
   > ![Adicionar membros do grupo](./media/sap-successfactors-inbound-provisioning/add-group-members.png)

1. Clique **em Fazer** para terminar a criação do Grupo de Permissões.

### <a name="grant-permission-role-to-the-permission-group"></a>Atribuir papel de permissão ao Grupo de Permissão

1. No SuccessFactors Admin Center, procure por *Manage Permission Roles*, em seguida, **selecione Gerir Funções** de Permisse a partir dos resultados da pesquisa.
1. A partir da **Lista de Funções de Permisse,** selecione a função que criou para permissões de utilização da API.
1. Sob **o Comando de Grant, este papel para...** clique em **Adicionar...** botão.
1. Selecione **Grupo de Permissões...** a partir do menu suspenso, em seguida, clique em **Selecionar...** para abrir a janela Grupos para procurar e selecionar o grupo criado acima. 

   > [!div class="mx-imgBorder"]
   > ![Adicionar grupo de permissões](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)

1. Reveja a concessão de função de permissão ao Grupo de Permissão. 
   > [!div class="mx-imgBorder"]
   > ![Papel de Permissão e detalhe de grupo](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)

1. Clique em **Guardar Alterações**.

## <a name="preparing-for-successfactors-writeback"></a>Preparação para o SucessoFactors Writeback

A aplicação de provisionamento de writeback SuccessFactors utiliza certos valores de *código* para definir números de e-mail e de telefone na Central dos Funcionários. Estes valores de *código* são definidos como valores constantes na tabela de mapeamento de atributos e são diferentes para cada instância SuccessFactors. Esta secção fornece passos para capturar estes valores de *código.*

   > [!NOTE]
   > Por favor, envolva o seu SuccessFactors Admin para completar os passos nesta secção. 

### <a name="identify-email-and-phone-number-picklist-names"></a>Identificar nomes de lista de e-mail e números de telefone 

No SAP SuccessFactors, uma *picklist* é um conjunto configurável de opções a partir das quais um utilizador pode fazer uma seleção. Os diferentes tipos de e-mail e número de telefone (por exemplo, negócios, pessoais, outros) estão representados através de uma picklist. Neste passo, identificaremos as listas de picklists configuradas no seu inquilino SuccessFactors para armazenar valores de e-mail e número de telefone. 
 
1. No SuccessFactors Admin Center, procure a *configuração de negócios de Manage*. 

   > [!div class="mx-imgBorder"]
   > ![Gerir a configuração de negócios](./media/sap-successfactors-inbound-provisioning/manage-business-config.png)

1. Nos **Elementos HRIS**, selecione **o emailInfo** e clique nos *Detalhes* para o campo do tipo **de e-mail.**

   > [!div class="mx-imgBorder"]
   > ![Obtenha informações de e-mail](./media/sap-successfactors-inbound-provisioning/get-email-info.png)

1. Na página de detalhes **do tipo e-mail,** anote o nome da lista de selecionadores associada a este campo. Por predefinição, é **ecEmailType**. No entanto, pode ser diferente no seu inquilino. 

   > [!div class="mx-imgBorder"]
   > ![Identificar lista de e-mails](./media/sap-successfactors-inbound-provisioning/identify-email-picklist.png)

1. Em **Elementos HRIS**, selecione **o telefoneInfo** e clique nos *Detalhes* para o campo do **tipo telefone.**

   > [!div class="mx-imgBorder"]
   > ![Obter informações de telefone](./media/sap-successfactors-inbound-provisioning/get-phone-info.png)

1. Na página de detalhes **do tipo telefone,** anote o nome da lista de selecionadores associada a este campo. Por predefinição, é **ecPhoneType**. No entanto, pode ser diferente no seu inquilino. 

   > [!div class="mx-imgBorder"]
   > ![Identificar lista de escolhas telefónicas](./media/sap-successfactors-inbound-provisioning/identify-phone-picklist.png)

### <a name="retrieve-constant-value-for-emailtype"></a>Recupere o valor constante para o e-mailType

1. No SuccessFactors Admin Center, procure e abra *o Picklist Center.* 
1. Utilize o nome da lista de e-mails capturada na secção anterior (por exemplo, ecEmailType) para encontrar a lista de e-mails. 

   > [!div class="mx-imgBorder"]
   > ![Encontre a lista de escolhas do tipo de e-mail](./media/sap-successfactors-inbound-provisioning/find-email-type-picklist.png)

1. Abra a lista de e-mails ativos. 

   > [!div class="mx-imgBorder"]
   > ![Abrir a lista de e-mails ativos](./media/sap-successfactors-inbound-provisioning/open-active-email-type-picklist.png)

1. Na página de picklist tipo de e-mail, selecione o tipo de e-mail *Business.*

   > [!div class="mx-imgBorder"]
   > ![Selecione o tipo de e-mail de negócios](./media/sap-successfactors-inbound-provisioning/select-business-email-type.png)

1. Note o **ID de opção** associado ao e-mail *do Negócio.* Este é o código que iremos usar com *e-mailType* na tabela de mapeamento de atributos.

   > [!div class="mx-imgBorder"]
   > ![Obtenha código de tipo de e-mail](./media/sap-successfactors-inbound-provisioning/get-email-type-code.png)

   > [!NOTE]
   > Largue o caractere de vírgula quando copiar sobre o valor. Por exemplo, se o valor **de ID de opção** for *de 8.448,* em seguida, defina o *e-mailType* em AD Azure para o número constante *8448* (sem o caráter de vírgula). 

### <a name="retrieve-constant-value-for-phonetype"></a>Recupere o valor constante para o telefoneType

1. No SuccessFactors Admin Center, procure e abra *o Picklist Center.* 
1. Utilize o nome da lista de escolhas telefónicas capturada na secção anterior para encontrar a lista de escolhas telefónicas. 

   > [!div class="mx-imgBorder"]
   > ![Encontre a lista de escolhas do tipo de telefone](./media/sap-successfactors-inbound-provisioning/find-phone-type-picklist.png)

1. Abra a lista de escolhas de telefones ativos. 

   > [!div class="mx-imgBorder"]
   > ![Abrir a lista de escolhas do tipo de telefone ativo](./media/sap-successfactors-inbound-provisioning/open-active-phone-type-picklist.png)

1. Na página da lista de escolhas do tipo telefone, reveja os diferentes tipos de telefone listados nos **Valores da Lista de Escolhas**.

   > [!div class="mx-imgBorder"]
   > ![Rever tipos de telefone](./media/sap-successfactors-inbound-provisioning/review-phone-types.png)

1. Note o **ID de opção** associado ao telefone *Business.* Este é o código que iremos usar com *o BusinessPhoneType* na tabela de mapeamento de atributos.

   > [!div class="mx-imgBorder"]
   > ![Obter código de telefone de negócios](./media/sap-successfactors-inbound-provisioning/get-business-phone-code.png)

1. Note o **ID de opção** associado *ao* telemóvel. Este é o código que iremos usar com *o CellPhoneType* na tabela de mapeamento de atributos.

   > [!div class="mx-imgBorder"]
   > ![Obtenha o código do telemóvel](./media/sap-successfactors-inbound-provisioning/get-cell-phone-code.png)

   > [!NOTE]
   > Largue o caractere de vírgula quando copiar sobre o valor. Por exemplo, se o valor **de ID de opção** for *de 10.606,* em seguida, coloque o *telemóvelPhoneType* em AD Azure para o número constante *10606* (sem o carácter de vírgula). 


## <a name="configuring-successfactors-writeback-app"></a>App de writeback de sucesso configurante

Esta secção fornece passos para 

* [Adicione a app de conector de provisionamento e configuure a conectividade aos SuccessFactors](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [Configurar mapeamentos de atributos](#part-2-configure-attribute-mappings)
* [Permitir e lançar o fornecimento de utilizadores](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>Parte 1: Adicione a app de conector de provisionamento e configuure a conectividade aos SuccessFactors

**Para configurar a gravação do SuccessFactors:**

1. Ir para <https://portal.azure.com>

2. Na barra de navegação à esquerda, selecione **Azure Ative Directory**

3. Selecione **Aplicações empresariais,** em seguida, **todas as aplicações**.

4. **Selecione Adicione uma aplicação** e selecione a categoria **'Todos'.**

5. Procure por **SuccessFactors Writeback** e adicione a aplicação na galeria.

6. Depois de adicionar a app e o ecrã de detalhes da aplicação ser mostrado, selecione **Provisioning**

7. Alterar o **modo** **de provisionamento** para **automático**

8. Preencha a secção **credenciais de administração** da seguinte forma:

   * **Nome de Utilizador Admin** – Introduza o nome de utilizador da conta de utilizador da API successFactors, com o ID da empresa anexado. Tem o formato: **\@ username companyID**

   * **Senha de administração –** Introduza a palavra-passe da conta de utilizador da API do SuccessFactors. 

   * **URL do inquilino –** Insira o nome do ponto final dos serviços OData API dos SuccessFactors. Apenas introduza o nome de anfitrião do servidor sem https ou https. Este valor deve parecer: `api4.successfactors.com` .

   * **E-mail de notificação –** Insira o seu endereço de e-mail e verifique a caixa de verificação "enviar e-mail se ocorrer falha".
    > [!NOTE]
    > O Serviço de Provisionamento Azure AD envia uma notificação por e-mail se o trabalho de provisionamento entrar em estado de [quarentena.](../app-provisioning/application-provisioning-quarantine-status.md)

   * Clique no botão **De Ligação de Teste.** Se o teste de ligação for bem sucedido, clique no botão **Guardar** na parte superior. Se falhar, verifique duas vezes se as credenciais e URL do SuccessFactors são válidos.
    >[!div class="mx-imgBorder"]
    >![Portal do Azure](./media/sap-successfactors-inbound-provisioning/sfwb-provisioning-creds.png)

   * Uma vez guardadas as credenciais com sucesso, a secção **Mappings** apresentará o mapeamento predefinido. Refresque a página, se os mapeamentos do atributo não forem visíveis.  

### <a name="part-2-configure-attribute-mappings"></a>Parte 2: Configurar mapeamentos de atributos

Nesta secção, irá configurar como os dados dos utilizadores fluem de SuccessFactors para Ative Directory.

1. No separador De Provisionamento em **Mappings,** clique em **Provision Azure Ative Directory Users**.

1. No campo **'Âmbito do Objeto fonte',** pode selecionar quais os conjuntos de utilizadores em Azure AD que devem ser considerados para desativação, definindo um conjunto de filtros baseados em atributos. O âmbito padrão é "todos os utilizadores em Azure AD". 
   > [!TIP]
   > Quando estiver a configurar a app de provisionamento pela primeira vez, terá de testar e verificar os mapeamentos e expressões do seu atributo para se certificar de que está a dar-lhe o resultado desejado. A Microsoft recomenda a utilização dos filtros de deteção no **âmbito do objeto de origem** para testar os seus mapeamentos com alguns utilizadores de teste do Azure AD. Depois de verificar que os mapeamentos funcionam, pode remover o filtro ou expandi-lo gradualmente para incluir mais utilizadores.

1. O campo **"Ações de Objecto-Alvo"** suporta apenas a operação **Atualização.**

1. Na tabela de mapeamento na secção **de mapeamentos Do Atributo,** pode mapear os seguintes atributos do Azure Ative Directory aos SuccessFactors. A tabela abaixo fornece orientações sobre como mapear os atributos de write-back. 

   | \# | Atributo do Azure AD | Atributo SuccessFactors | Observações |
   |--|--|--|--|
   | 1 | employeeId | personIdExternal | Por predefinição, este atributo é o identificador correspondente. Em vez de employeeId, pode utilizar qualquer outro atributo AD Azure que possa armazenar o valor igual ao personIdExternal em SuccessFactors.    |
   | 2 | correio | e-mail | Mapa e-mail atributo fonte. Para efeitos de teste, pode mapear o nome do utilizadorPrincipal Para e-mail. |
   | 3 | 8448 | e-mailType | Este valor constante é o valor de ID Do SuccessFactors associado ao e-mail de negócios. Atualize este valor para corresponder ao ambiente SuccessFactors. Consulte a secção Recuperar valor [constante para emailType](#retrieve-constant-value-for-emailtype) para etapas para definir este valor. |
   | 4 | true | e-mailIsPrimary | Utilize este atributo para definir o email de negócios como principal nos SuccessFactors. Se o e-mail de negócios não for primário, coloque esta bandeira em falso. |
   | 5 | userPrincipalName | [custom01 – custom15] | Utilizando **Add New Mapping**, pode escrever opcionalmente o userPrincipalName ou qualquer atributo AD Azure a um atributo personalizado disponível no objeto do Utilizador SuccessFactors.  |
   | 6 | on-prem-samAccountName | nome de utilizador | Utilizando **o Add New Mapping,** pode mapear opcionalmente no local o nome de nome de utilizador DoFactcount Para o SuccessFactors. |
   | 7 | SSO | loginMethod | Se o inquilino SuccessFactors estiver configurado para [SSO parcial,](https://apps.support.sap.com/sap/support/knowledge/en/2320766)em seguida, usando Add New Mapping, pode configurar opcionalmente loginMethod para um valor constante de "SSO" ou "PWD". |
   | 8 | número de telefone | businessPhoneNumber | Utilize este mapeamento para fluir *o telefoneNumber* de Azure AD para SuccessFactors negócio /número de telefone de trabalho. |
   | 9 | 10605 | BusinessPhoneType | Este valor constante é o valor de ID SuccessFactors associado ao telefone comercial. Atualize este valor para corresponder ao ambiente SuccessFactors. Consulte a secção Recuperar valor [constante para telefoneType](#retrieve-constant-value-for-phonetype) para etapas para definir este valor. |
   | 10 | true | businessPhoneIsPrimary | Utilize este atributo para definir a bandeira principal para o número de telefone comercial. Valores válidos são verdadeiros ou falsos. |
   | 11 | dispositivo móvel | cellPhoneNumber | Utilize este mapeamento para fluir *o telefoneNumber* de Azure AD para SuccessFactors negócio /número de telefone de trabalho. |
   | 12 | 10606 | telemóvelType | Este valor constante é o valor de ID SuccessFactors associado ao telemóvel. Atualize este valor para corresponder ao ambiente SuccessFactors. Consulte a secção Recuperar valor [constante para telefoneType](#retrieve-constant-value-for-phonetype) para etapas para definir este valor. |
   | 13 | false | cellPhoneIsPrimary | Utilize este atributo para definir a bandeira primária para o número de telemóvel. Valores válidos são verdadeiros ou falsos. |
 
1. Valide e reveja os mapeamentos de atributos. 
 
    >[!div class="mx-imgBorder"]
    >![Mapeamento de atributo de writeback](./media/sap-successfactors-inbound-provisioning/writeback-attribute-mapping.png)

1. Clique **em Guardar** para guardar os mapeamentos. Em seguida, atualizaremos as expressões API do Caminho JSON para usar os códigos phoneType na sua instância SuccessFactors. 
1. Selecione **Mostrar opções avançadas**. 

    >[!div class="mx-imgBorder"]
    >![Mostrar opções avançadas](./media/sap-successfactors-inbound-provisioning/show-advanced-options.png)

1. Clique na **lista de atributos Editar para SuccessFactors**. 

   > [!NOTE] 
   > Se a lista de atributos Editar para a opção **SuccessFactors** não aparecer no portal Azure, utilize o URL *https://portal.azure.com/?Microsoft_AAD_IAM_forceSchemaEditorEnabled=true* para aceder à página. 

1. A coluna **de expressão API** nesta vista mostra as expressões do Caminho JSON utilizadas pelo conector. 
1. Atualize as expressões JSON Path para telefone comercial e telemóvel para utilizar o valor ID *(businessPhoneType* e *cellPhoneType*) correspondente ao seu ambiente. 

    >[!div class="mx-imgBorder"]
    >![Mudança de caminho do telefone JSON](./media/sap-successfactors-inbound-provisioning/phone-json-path-change.png)

1. Clique **em Guardar** para guardar os mapeamentos.

## <a name="enable-and-launch-user-provisioning"></a>Permitir e lançar o fornecimento de utilizadores

Uma vez concluídas as configurações de aplicações do SuccessFactors, pode ligar o serviço de prestação no portal Azure.

> [!TIP]
> Por padrão, quando ligar o serviço de fornecimento, iniciará operações de provisionamento para todos os utilizadores no âmbito. Se houver erros no mapeamento ou problemas de dados, então o trabalho de provisionamento pode falhar e entrar no estado de quarentena. Para evitar isto, como uma boa prática, recomendamos configurar o filtro **Source Object Scope** e testar os mapeamentos do seu atributo com alguns utilizadores de teste antes de lançar a sincronização completa para todos os utilizadores. Uma vez verificado que os mapeamentos funcionam e lhe estão a dar os resultados desejados, então pode remover o filtro ou expandi-lo gradualmente para incluir mais utilizadores.

1. No **separador Provisioning,** desa fixação do **Estado de Provisionamento** para **On**.

1. Selecione **Âmbito**. Pode selecionar a partir de uma das seguintes opções: 
   * **Sincronize todos os utilizadores e grupos**: Selecione esta opção se pretender escrever atributos mapeados de todos os utilizadores, desde Azure AD a SuccessFactors, sujeitos às regras de deteção definidas no Âmbito do Objeto fonte **de Mapeamentos.**  ->  **Source Object Scope** 
   * **Sincronizar apenas utilizadores e grupos atribuídos**: Selecione esta opção se pretender rescrever atributos mapeados de apenas utilizadores que atribuiu a esta aplicação na opção de menu De gestão de **aplicações**  ->  **Manage**  ->  **e grupos.** Estes utilizadores também estão sujeitos às regras de deteção definidas no âmbito do objeto de **origem de mapeamentos.**  ->  **Source Object Scope**

   > [!div class="mx-imgBorder"]
   > ![Selecione o âmbito de writeback](./media/sap-successfactors-inbound-provisioning/select-writeback-scope.png)

   > [!NOTE]
   > A app de provisioning SuccessFactors Writeback não suporta a "atribuição de grupo". Apenas é suportada a "atribuição do utilizador". 

1. Clique em **Guardar**.

1. Esta operação iniciará a sincronização inicial, que pode demorar um número variável de horas dependendo de quantos utilizadores estão no inquilino AZure AD e no âmbito definido para a operação. Pode verificar a barra de progresso para acompanhar o progresso do ciclo de sincronização. 

1. A qualquer momento, consulte o **separador De provisionamento** no portal Azure para ver que ações o serviço de fornecimento executou. Os registos de fornecimento listam todos os eventos sincronizados individuais realizados pelo serviço de fornecimento. 

1. Uma vez concluída a sincronização inicial, escreverá um relatório de resumo de auditoria no **separador Provisioning,** como mostrado abaixo.

   > [!div class="mx-imgBorder"]
   > ![Provisão de barras de progresso](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="supported-scenarios-known-issues-and-limitations"></a>Cenários apoiados, questões e limitações conhecidas

Consulte a [secção de cenários writeback](../app-provisioning/sap-successfactors-integration-reference.md#writeback-scenarios) do guia de referência de integração SAP SuccessFactors. 

## <a name="next-steps"></a>Passos seguintes

* [Mergulho profundo na Azure AD e NAUS SuccessFactors referência de integração](../app-provisioning/sap-successfactors-integration-reference.md)
* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)
* [Saiba como configurar um único sign-on entre SuccessFactors e Azure Ative Directory](successfactors-tutorial.md)
* [Saiba como integrar outras aplicações saaS com o Azure Ative Directory](tutorial-list.md)
* [Saiba como exportar e importar as suas configurações de provisionamento](../app-provisioning/export-import-provisioning-configuration.md)