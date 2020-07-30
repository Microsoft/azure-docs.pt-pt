---
title: 'Tutorial: Configurar a gravação do SAP SuccessFactors no Azure Ative Directory Microsoft Docs'
description: Saiba como configurar o atributo de volta aos SAP SuccessFactors da Azure AD
services: active-directory
author: cmmdesai
documentationcenter: na
manager: jodadzie
ms.assetid: ad255bd4-9e50-43a1-a92b-359215868b6b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/23/2020
ms.author: chmutali
ms.openlocfilehash: 5181cc0f6457c5eb98cca828ecb91c52f47f3fbe
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87273545"
---
# <a name="tutorial-configure-attribute-write-back-from-azure-ad-to-sap-successfactors-preview"></a>Tutorial: Configure atribui write-back de Azure AD a SAP SuccessFactors (Preview)
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

* Organizações que usam o Office 365 que desejam rescrever atributos autoritários geridos por TI (como endereço de e-mail, telefone, nome de utilizador) de volta à SuccessFactors Employee Central.

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
1. Procure por *Gerir funções de permissões*e, em seguida, **selecione 'Gerir funções'** a partir dos resultados da pesquisa.

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

1. No Centro de Administração SuccessFactors, procure *grupos de permissões de gestão*e, em seguida, selecione **'Gerir grupos de permissão'** a partir dos resultados da pesquisa.

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

A aplicação de provisionamento de writeback SuccessFactors utiliza certos valores de *código* para definir números de e-mail e de telefone na Central dos Funcionários. Estes valores de *código* são definidos como valores constantes na tabela de mapeamento de atributos e são diferentes para cada instância SuccessFactors. Esta secção usa [o Carteiro](https://www.postman.com/downloads/) para obter os valores de código. Pode utilizar [cURL,](https://curl.haxx.se/) [Fiddler](https://www.telerik.com/fiddler) ou qualquer outra ferramenta similar para enviar pedidos HTTP. 

### <a name="download-and-configure-postman-with-your-successfactors-tenant"></a>Baixe e configuure o Carteiro com o seu inquilino SuccessFactors

1. Baixar [Carteiro](https://www.postman.com/downloads/)
1. Crie uma "Nova Coleção" na aplicação Do Carteiro. Chame-lhe "SuccessFactors". 

   > [!div class="mx-imgBorder"]
   > ![Nova coleção do Carteiro](./media/sap-successfactors-inbound-provisioning/new-postman-collection.png)

1. No separador "Autorização", introduza as credenciais do utilizador da API configuradas na secção anterior. Tipo configurar como "Auth Básico". 

   > [!div class="mx-imgBorder"]
   > ![Autorização do carteiro](./media/sap-successfactors-inbound-provisioning/postman-authorization.png)

1. Guarde a configuração. 

### <a name="retrieve-constant-value-for-emailtype"></a>Recupere o valor constante para o e-mailType

1. No Carteiro, clique na elipse (...) associada à coleção SuccessFactors e adicione um "Novo Pedido" chamado "Obter Tipos de E-mail" como mostrado abaixo. 

   > [!div class="mx-imgBorder"]
   > ![Pedido de e-mail do carteiro](./media/sap-successfactors-inbound-provisioning/postman-email-request.png)

1. Abra o painel de pedidos "Get Email Type". 
1. No URL GET, adicione o seguinte URL, substituindo `successFactorsAPITenantName` pelo inquilino da API para a sua instância SuccessFactors. 
   `https://<successfactorsAPITenantName>/odata/v2/Picklist('ecEmailType')?$expand=picklistOptions&$select=picklistOptions/id,picklistOptions/externalCode&$format=json`

   > [!div class="mx-imgBorder"]
   > ![Carteiro obter tipo de e-mail](./media/sap-successfactors-inbound-provisioning/postman-get-email-type.png)

1. O separador "Autorização" herdará a auth configurada para a coleção. 
1. Clique em "Enviar" para invocar a chamada da API. 
1. No organismo Resposta, veja o conjunto de resultados JSON e procure o ID correspondente ao `externalCode = B` . 

   > [!div class="mx-imgBorder"]
   > ![Resposta do tipo de e-mail do carteiro](./media/sap-successfactors-inbound-provisioning/postman-email-type-response.png)

1. Note este valor como a constante a usar com *e-mailType* na tabela de mapeamento de atributos.

### <a name="retrieve-constant-value-for-phonetype"></a>Recupere o valor constante para o telefoneType

1. No Carteiro, clique na elipse (...) associada à coleção SuccessFactors e adicione um "Novo Pedido" chamado "Obter Tipos de Telefone" como mostrado abaixo. 

   > [!div class="mx-imgBorder"]
   > ![Pedido de telefone do carteiro](./media/sap-successfactors-inbound-provisioning/postman-phone-request.png)

1. Abra o painel de pedidos "Get Phone Type". 
1. No URL GET, adicione o seguinte URL, substituindo `successFactorsAPITenantName` pelo inquilino da API para a sua instância SuccessFactors. 
   `https://<successfactorsAPITenantName>/odata/v2/Picklist('ecPhoneType')?$expand=picklistOptions&$select=picklistOptions/id,picklistOptions/externalCode&$format=json`

   > [!div class="mx-imgBorder"]
   > ![Carteiro obter tipo de telefone](./media/sap-successfactors-inbound-provisioning/postman-get-phone-type.png)

1. O separador "Autorização" herdará a auth configurada para a coleção. 
1. Clique em "Enviar" para invocar a chamada da API. 
1. No organismo Resposta, veja o conjunto de resultados JSON e procure o *id* correspondente ao `externalCode = B` e `externalCode = C` . 

   > [!div class="mx-imgBorder"]
   > ![Carteiro-Telefone](./media/sap-successfactors-inbound-provisioning/postman-phone-type-response.png)

1. Note estes valores como as constantes a utilizar com *o BusinessPhoneType* e *o cellPhoneType* na tabela de mapeamento de atributos.

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

4. **Selecione Adicione uma aplicação**e selecione a categoria **'Todos'.**

5. Procure por **SuccessFactors Writeback**e adicione a aplicação na galeria.

6. Depois de adicionar a app e o ecrã de detalhes da aplicação ser mostrado, selecione **Provisioning**

7. Alterar o **modo** **de provisionamento** para **automático**

8. Preencha a secção **credenciais de administração** da seguinte forma:

   * **Nome de Utilizador Admin** – Introduza o nome de utilizador da conta de utilizador da API successFactors, com o ID da empresa anexado. Tem o formato: ** \@ username companyID**

   * **Senha de administração –** Introduza a palavra-passe da conta de utilizador da API do SuccessFactors. 

   * **URL do inquilino –** Insira o nome do ponto final dos serviços OData API dos SuccessFactors. Apenas introduza o nome de anfitrião do servidor sem https ou https. Este valor deve parecer: `api4.successfactors.com` .

   * **E-mail de notificação –** Insira o seu endereço de e-mail e verifique a caixa de verificação "enviar e-mail se ocorrer falha".
    > [!NOTE]
    > O Serviço de Provisionamento Azure AD envia uma notificação por e-mail se o trabalho de provisionamento entrar em estado de [quarentena.](/azure/active-directory/manage-apps/application-provisioning-quarantine-status)

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

2. Clique em **Save** (Guardar).

3. Esta operação iniciará a sincronização inicial, que pode demorar um número variável de horas dependendo de quantos utilizadores estão no inquilino successFactors. Pode verificar a barra de progresso para acompanhar o progresso do ciclo de sincronização. 

4. A qualquer momento, consulte o **separador de registos** de auditoria no portal Azure para ver que ações o serviço de prestação de serviços executou. Os registos de auditoria listam todos os eventos de sincronização individuais realizados pelo serviço de fornecimento, tais como os utilizadores que estão a ser lidos a partir da Central dos Funcionários e posteriormente adicionados ou atualizados ao Ative Directory. 

5. Uma vez concluída a sincronização inicial, escreverá um relatório de resumo de auditoria no **separador Provisioning,** como mostrado abaixo.

   > [!div class="mx-imgBorder"]
   > ![Provisão de barras de progresso](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="supported-scenarios-known-issues-and-limitations"></a>Cenários apoiados, questões e limitações conhecidas

Consulte a [secção de cenários writeback](../app-provisioning/sap-successfactors-integration-reference.md#writeback-scenarios) do guia de referência de integração SAP SuccessFactors. 

## <a name="next-steps"></a>Próximos passos

* [Mergulho profundo na Azure AD e NAUS SuccessFactors referência de integração](../app-provisioning/sap-successfactors-integration-reference.md)
* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
* [Saiba como configurar um único sign-on entre SuccessFactors e Azure Ative Directory](successfactors-tutorial.md)
* [Saiba como integrar outras aplicações saaS com o Azure Ative Directory](tutorial-list.md)
* [Saiba como exportar e importar as suas configurações de provisionamento](../app-provisioning/export-import-provisioning-configuration.md)

