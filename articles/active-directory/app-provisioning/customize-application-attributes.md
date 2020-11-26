---
title: Tutorial - Personalize mapeamentos de atributos Azure Ative Directory
description: Saiba quais os mapeamentos de atributos para aplicações SaaS no Azure Ative Directory como pode modificá-las para atender às necessidades do seu negócio.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: tutorial
ms.date: 11/10/2020
ms.author: kenwith
ms.openlocfilehash: 5f6e5de61c9f43d5958e68c545105c4373996f7c
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96175071"
---
# <a name="tutorial---customize-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>Tutorial - Personalize o fornecimento de produtos de fornecimento de utilizadores para aplicações SaaS no Azure Ative Directory

O Microsoft Azure AD fornece suporte para o fornecimento de utilizadores a aplicações SaaS de terceiros, tais como Salesforce, G Suite e outros. Se ativar o fornecimento de um pedido saaS de terceiros, o portal Azure controla os seus valores de atributos através de mapeamentos de atributos.

Há um conjunto pré-configurado de atributos e mapeamentos de atributos entre objetos de utilizador AD Azure e objetos de utilizador de cada aplicação SaaS. Algumas aplicações gerem outros tipos de objetos juntamente com os Utilizadores, como grupos.

Pode personalizar os mapeamentos de atributos padrão de acordo com as necessidades do seu negócio. Assim, pode alterar ou eliminar os mapeamentos de atributos existentes ou criar novos mapeamentos de atributos.

## <a name="editing-user-attribute-mappings"></a>Edição de mapeamentos de atributos do utilizador

Siga estes passos para aceder à funcionalidade **Mappings** do fornecimento do utilizador:

1. Inscreva-se no [portal Azure Ative Directory](https://aad.portal.azure.com).
1. Selecione **aplicações** Enterprise a partir do painel esquerdo. É mostrada uma lista de todas as aplicações configuradas, incluindo aplicações que foram adicionadas da galeria.
1. Selecione qualquer aplicação para carregar o painel de gestão de aplicações, onde pode ver relatórios e gerir as definições de aplicações.
1. **Selecione Provisioning** para gerir as definições de provisão de conta de utilizador para a aplicação selecionada.
1. Expanda **os Mapeamentos** para visualizar e editar os atributos do utilizador que fluem entre a Azure AD e a aplicação-alvo. Se a aplicação-alvo o suportar, esta secção permite configurar opcionalmente o fornecimento de grupos e contas de utilizador.

   ![Use Mapeamentos para visualizar e editar atributos do utilizador](./media/customize-application-attributes/21.png)

1. Selecione uma configuração **mapping para** abrir o ecrã de **mapeamento de atributos** relacionado. Alguns mapeamentos de atributos são necessários por uma aplicação SaaS para funcionar corretamente. Para os atributos necessários, a função **Eliminar** não está disponível.

   ![Utilizar o 'Mapping' do Atributo para configurar mapeamentos de atributos para apps](./media/customize-application-attributes/22.png)

   Nesta imagem, pode ver que o atributo **Username** de um objeto gerido no Salesforce é povoado com o valor do nome de **utilizadorPrincipalName** do Azure Ative Directory Object ligado.

1. Selecione um **mapeamento** de atributos existente para abrir o ecrã **EditAr Atributo.** Aqui pode editar os atributos do utilizador que fluem entre a Azure AD e a aplicação-alvo.

   ![Use Edit Edit Attribute para editar atributos do utilizador](./media/customize-application-attributes/23.png)

### <a name="understanding-attribute-mapping-types"></a>Compreender tipos de mapeamento de atributos

Com os mapeamentos de atributos, você controla como os atributos são povoados numa aplicação SaaS de terceiros.
Existem quatro tipos de mapeamento diferentes suportados:

- **Direto** – o atributo alvo é povoado com o valor de um atributo do objeto ligado em Azure AD.
- **Constante** – o atributo alvo é preenchido com uma cadeia específica especificada.
- **Expressão** - o atributo alvo é povoado com base no resultado de uma expressão semelhante ao script.
  Para obter mais informações, consulte [a Escrita Expressões para Attribute-Mappings no Diretório Ativo Azure](../app-provisioning/functions-for-customizing-application-data.md).
- **Nenhum** - o atributo alvo é deixado sem modificação. No entanto, se o atributo alvo estiver sempre vazio, é preenchido com o valor padrão que especifica.

Juntamente com estes quatro tipos básicos, os mapeamentos de atributos personalizados suportam o conceito de uma atribuição de valor **padrão** opcional. A atribuição de valor predefinido garante que um atributo alvo é povoado com um valor se não houver um valor em Ad Azure ou no objeto alvo. A configuração mais comum é deixar este em branco.

### <a name="understanding-attribute-mapping-properties"></a>Compreender propriedades de mapeamento de atributos

Na secção anterior, já foi introduzido na propriedade do tipo de mapeamento de atributos.
Juntamente com esta propriedade, os mapeamentos de atributos também suportam os seguintes atributos:

- **Atributo de origem** - O atributo do utilizador a partir do sistema de origem (exemplo: Azure Ative Directory).
- **Atributo-alvo** – O atributo do utilizador no sistema-alvo (exemplo: ServiceNow).
- **Valor predefinido se nulo (opcional)** - O valor que será passado para o sistema-alvo se o atributo de origem for nulo. Este valor só será a provisionado quando um utilizador for criado. O "valor predefinido quando nulo" não será previsto ao atualizar um utilizador existente. Se, por exemplo, pretender que todos os utilizadores existentes no sistema-alvo se encontrem com um determinado Título de Trabalho (quando é nulo no sistema de origem), pode utilizar a seguinte [expressão](../app-provisioning/functions-for-customizing-application-data.md): Switch(IsPresent([jobTitle]), "DefaultValue", "True", [jobTitle]). Certifique-se de que substitui o "Valor Predefinido" pelo que pretende prever quando forlo nulo no sistema de origem. 
- **Combine os objetos que utilizam este atributo** – Se este mapeamento deve ser utilizado para identificar exclusivamente os utilizadores entre os sistemas de origem e alvo. É tipicamente definido no nome de utilizadorPrincipalName ou atributo de correio em Azure AD, que é tipicamente mapeado para um campo de nome de utilizador em uma aplicação alvo.
- **Precedência correspondente** - Podem ser definidos vários atributos correspondentes. Quando há múltiplos, são avaliados na ordem definida por este campo. Assim que um fósforo é encontrado, não são avaliados mais atributos correspondentes. Embora possa definir quantos atributos correspondentes quiser, considere se os atributos que está a usar como atributos correspondentes são verdadeiramente únicos e precisam de ser atributos correspondentes. Geralmente, os clientes têm 1 ou 2 atributos correspondentes na sua configuração. 
- **Aplique este mapeamento**
  - **Sempre** – Aplique este mapeamento tanto na criação do utilizador como nas ações de atualização.
  - **Apenas durante a criação** - Aplique este mapeamento apenas nas ações de criação de utilizadores.

## <a name="matching-users-in-the-source-and-target--systems"></a>Utilizadores correspondentes nos sistemas de origem e alvo
O serviço de fornecimento de Azure AD pode ser implementado tanto em cenários de "campo verde" (onde os utilizadores não saem no sistema alvo) como em cenários de "brownfield" (onde os utilizadores já existem no sistema-alvo). Para suportar ambos os cenários, o serviço de fornecimento utiliza o conceito de atributos correspondentes. Os atributos correspondentes permitem-lhe determinar como identificar um utilizador de forma única na fonte e corresponder ao utilizador no alvo. Como parte do planeamento da sua implementação, identifique o atributo que pode ser usado para identificar exclusivamente um utilizador nos sistemas de origem e alvo. Coisas a notar:

- **Os atributos correspondentes devem ser únicos:** Os clientes usam frequentemente atributos como userPrincipalName, mail ou object ID como o atributo correspondente.
- **Vários atributos podem ser usados como atributos correspondentes:** Pode definir vários atributos a avaliar ao combinar utilizadores e a ordem na qual são avaliados (definidos como precedência correspondente na UI). Se, por exemplo, definir três atributos como atributos correspondentes, e um utilizador for exclusivamente compatível após a avaliação dos dois primeiros atributos, o serviço não avaliará o terceiro atributo. O serviço avaliará os atributos correspondentes na ordem especificada e deixará de avaliar quando uma correspondência for encontrada.  
- **O valor na fonte e no alvo não tem de corresponder exatamente:** O valor no alvo pode ser uma função simples do valor na fonte. Assim, pode-se ter um atributo de e-mailApo na fonte e no nome do utilizadorPrincipalName no alvo, e corresponder por uma função do atributo emailAddress que substitui alguns caracteres por algum valor constante.  
- **A correspondência com base numa combinação de atributos não é suportada:** A maioria das aplicações não suporta consultas com base em duas propriedades. Portanto, não é possível combinar com base numa combinação de atributos. É possível avaliar propriedades individuais após outra.
- **Todos os utilizadores devem ter um valor para pelo menos um atributo correspondente:** Se definir um atributo correspondente, todos os utilizadores devem ter um valor para esse atributo no sistema de origem. Se, por exemplo, definir o nome de utilizadorPrincipal como o atributo correspondente, todos os utilizadores devem ter um nome de utilizadorPrincipal. Se definir vários atributos de correspondência (por exemplo, extensãoTribute1 e correio), nem todos os utilizadores têm de ter o mesmo atributo de correspondência. Um utilizador pode ter uma extensãoAtribute1 mas não correio enquanto outro utilizador pode ter correio, mas sem extensãoAttribute1. 
- **A aplicação-alvo deve suportar a filtragem no atributo correspondente:** Os desenvolvedores de aplicações permitem a filtragem de um subconjunto de atributos no seu utilizador ou grupo API. Para aplicações na galeria, garantimos que o mapeamento de atributos predefinidos é para um atributo que a API da aplicação-alvo suporta a filtragem. Ao alterar o atributo de correspondência padrão para a aplicação-alvo, consulte a documentação da API de terceiros para garantir que o atributo pode ser filtrado.  

## <a name="editing-group-attribute-mappings"></a>Edição de atributos de grupo-mapeamentos

Um número selecionado de aplicações, tais como ServiceNow, Box e G Suite, suportam a capacidade de providenciar objetos de grupo e objetos do utilizador. Os objetos de grupo podem conter propriedades de grupo, tais como nomes de exibição e pseudónimos de e-mail, juntamente com membros do grupo.

![Exemplo mostra ServiceNow com objetos do Grupo e do Utilizador a provisionados](./media/customize-application-attributes/24.png)

O provisionamento em grupo pode ser opcionalmente ativado ou desativado selecionando o mapeamento de grupo em **Mapeamentos**, e definição **Ativada** à opção desejada no ecrã **de mapeamento** do atributo.

Os atributos previstos como parte dos objetos do Grupo podem ser personalizados da mesma forma que os objetos do Utilizador, descritos anteriormente. 

> [!TIP]
> O fornecimento de objetos de grupo (propriedades e membros) é um conceito distinto de [atribuir grupos](../manage-apps/assign-user-or-group-access-portal.md) a uma aplicação. É possível atribuir um grupo a uma aplicação, mas apenas providenciar os objetos de utilizador contidos no grupo. O fornecimento de objetos de grupo completos não é necessário para utilizar grupos em atribuições.

## <a name="editing-the-list-of-supported-attributes"></a>Edição da lista de atributos suportados

Os atributos do utilizador suportados para uma determinada aplicação estão pré-configurados. A maioria das APIs de gestão de utilizadores da aplicação não suportam a descoberta de esquemas. Assim, o serviço de fornecimento de AD Azure não é capaz de gerar dinamicamente a lista de atributos suportados fazendo chamadas para a aplicação.

No entanto, algumas aplicações suportam atributos personalizados, e o serviço de fornecimento AZURE AD pode ler e escrever para atributos personalizados. Para introduzir as suas definições no portal Azure, selecione a caixa de verificação de **opções avançadas do Show** na parte inferior do ecrã **de mapeamento** de atributos e, em seguida, selecione a lista de **atributos Editar para** a sua aplicação.

As aplicações e sistemas que suportam a personalização da lista de atributos incluem:

- Salesforce
- ServiceNow
- Dia de trabalho para Diretório Ativo / Dia de Trabalho para Azure Ative Directory
- SucessoFactors para Diretório Ativo / SuccessFactors para Azure Ative Directory
- Azure Ative Directy[(São suportados atributos padrão AZURE ADGraph API](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#user-entity) e extensões de diretório personalizados)
- Aplicativos que [suportam SCIM 2.0](https://tools.ietf.org/html/rfc7643)
- Para a writeback do Azure Ative Directory para Workday ou SuccessFactors, é suportado para atualizar metadados relevantes para atributos suportados (XPATH e JSONPath), mas não é suportado para adicionar novos atributos Workday ou SuccessFactors para além dos incluídos no esquema padrão


> [!NOTE]
> A edição da lista de atributos suportados é apenas recomendada para administradores que personalizaram o esquema das suas aplicações e sistemas, e têm conhecimento em primeira mão de como os seus atributos personalizados foram definidos. Isto por vezes requer familiaridade com as APIs e ferramentas de desenvolvimento fornecidas por uma aplicação ou sistema.

Ao editar a lista de atributos suportados, são fornecidas as seguintes propriedades:

- **Nome** - O nome do sistema do atributo, tal como definido no esquema do objeto-alvo.
- **Tipo** - O tipo de dados que o atributo armazena, tal como definido no esquema do objeto-alvo, que pode ser um dos seguintes tipos:
  - *Binário* - O atributo contém dados binários.
  - *Boolean* - Atributo contém um valor verdadeiro ou falso.
  - *DataTime* - O atributo contém uma cadeia de datas.
  - *Inteiro -* Atributo contém um inteiro.
  - *Referência* - O Atributo contém um ID que faz referência a um valor armazenado noutra tabela na aplicação-alvo.
  - *String*  - Atributo contém uma cadeia de texto.
- **Chave Primária?** - Se o atributo é definido como um campo chave primário no esquema do objeto alvo.
- **Necessário?** - Se o atributo é necessário para ser povoado na aplicação ou sistema-alvo.
- **Multi-valor?** - Se o atributo suporta vários valores.
- **Caso exato?** - Se os valores dos atributos são avaliados de forma sensível a casos.
- **Expressão API** - Não utilize, a menos que seja instruído pela documentação de um conector específico de provisionamento (como o Workday).
- **Atributo de objeto referenciado** - Se for um atributo do tipo referência, este menu permite selecionar a tabela e atribuir na aplicação-alvo que contém o valor associado ao atributo. Por exemplo, se tiver um atributo chamado "Departamento" cujo valor armazenado referencia um objeto numa tabela separada de "Departamentos", escolherá "Departments.Name". As tabelas de referência e os campos de ID primários suportados para uma determinada aplicação são pré-configurados e atualmente não podem ser editados usando o portal Azure, mas podem ser editados usando a [API](/graph/api/resources/synchronization-configure-with-custom-target-attributes)do Gráfico microsoft .

#### <a name="provisioning-a-custom-extension-attribute-to-a-scim-compliant-application"></a>Provisionando um atributo de extensão personalizada a uma aplicação compatível com o SCIM
O SCIM RFC define um esquema de utilizador e grupo principal, ao mesmo tempo que permite extensões ao esquema para atender às necessidades da sua aplicação. Para adicionar um atributo personalizado a uma aplicação SCIM:
   1. Inscreva-se no [portal Azure Ative Directory](https://aad.portal.azure.com), selecione **Aplicações empresariais,** selecione a sua aplicação e, em seguida, selecione **Provisioning**.
   2. Em **Mapeamentos,** selecione o objeto (utilizador ou grupo) para o qual pretende adicionar um atributo personalizado.
   3. Na parte inferior da página, selecione **Mostrar opções avançadas**.
   4. **Selecione a lista de atributos editar para AppName**.
   5. Na parte inferior da lista de atributos, insira informações sobre o atributo personalizado nos campos fornecidos. Em seguida, **selecione Adicionar Atributo**.

Para aplicações SCIM, o nome do atributo deve seguir o padrão indicado no exemplo abaixo. O "CustomExtensionName" e "CustomAttribute" podem ser personalizados de acordo com os requisitos da sua aplicação, por exemplo: urna:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User:CustomAttribute 

Estas instruções só são aplicáveis às aplicações ativadas pelo SCIM. Aplicações como ServiceNow e Salesforce não estão integradas com Azure AD usando SCIM, e, portanto, não requerem este espaço de nome específico ao adicionar um atributo personalizado.

Os atributos personalizados não podem ser atributos referenciais, atributos multi-valor ou de tipo complexo. Os atributos personalizados de extensão multi-valor e complexos são atualmente suportados apenas para aplicações na galeria.  
 
**Exemplo de representação de um utilizador com um atributo de extensão:**

```json
   {
     "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User",
      "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User",
      "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User"],
     "userName":"bjensen",
     "externalId":"bjensen",
     "name":{
       "formatted":"Ms. Barbara J Jensen III",
       "familyName":"Jensen",
       "givenName":"Barbara"
     },
     "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User": {
     "employeeNumber": "701984",
     "costCenter": "4130",
     "organization": "Universal Studios",
     "division": "Theme Park",
     "department": "Tour Operations",
     "manager": {
       "value": "26118915-6090-4610-87e4-49d8ca9f808d",
       "$ref": "../Users/26118915-6090-4610-87e4-49d8ca9f808d",
       "displayName": "John Smith"
     }
   },
     "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User": {
     "CustomAttribute": "701984",
   },
   "meta": {
     "resourceType": "User",
     "created": "2010-01-23T04:56:22Z",
     "lastModified": "2011-05-13T04:42:34Z",
     "version": "W\/\"3694e05e9dff591\"",
     "location":
 "https://example.com/v2/Users/2819c223-7f76-453a-919d-413861904646"
   }
 }
```


## <a name="provisioning-a-role-to-a-scim-app"></a>Provisionando um papel para uma aplicação SCIM
Utilize os passos abaixo para as funções de provisão para um utilizador à sua aplicação. Note que a descrição abaixo é específica para aplicações SCIM personalizadas. Para aplicações de galeria como Salesforce e ServiceNow, utilize os mapeamentos de funções pré-definidos. As balas abaixo descrevem como transformar as assinaturas appRole Atribuem ao formato que a sua aplicação espera.

- Mapear uma assinatura appRoleAssignment em Azure AD para um papel na sua aplicação requer que você transforme o atributo usando uma [expressão](../app-provisioning/functions-for-customizing-application-data.md). O atributo appRoleAssignment **não deve ser mapeado diretamente** a um atributo de função sem usar uma expressão para analisar os detalhes do papel. 

- **Assinatura SingleAppRoleAssignment** 
  - **Quando usar:** Utilize a expressão SingleAppRoleAssignment para prever uma única função para um utilizador e especificar o papel principal. 
  - **Como configurar:** Utilize os passos acima descritos para navegar na página de mapeamentos de atributos e use a expressão SingleAppRoleAssignment para mapear para o atributo de funções. Existem três atributos de papel a escolher: (funções[primary eq "True"].display, papéis[eq primário "True].type, e roles[primary eq "True"].value). Pode optar por incluir qualquer ou todos os atributos de função nos seus mapeamentos. Se quiser incluir mais do que um, basta adicionar um novo mapeamento e incluí-lo como o atributo alvo.  
  
  ![Adicionar singleAppRoleAssignment](./media/customize-application-attributes/edit-attribute-singleapproleassignment.png)
  - **Coisas a considerar**
    - Certifique-se de que várias funções não são atribuídas a um utilizador. Não podemos garantir que papel será aussitado.
    
  - **Pedido de exemplo (POST)** 

   ```json
    {
      "schemas": [
          "urn:ietf:params:scim:schemas:core:2.0:User"
      ],
      "externalId": "alias",
      "userName": "alias@contoso.OnMicrosoft.com",
      "active": true,
      "displayName": "First Name Last Name",
      "meta": {
           "resourceType": "User"
      },
      "roles": [
         {
               "primary": true,
               "type": "WindowsAzureActiveDirectoryRole",
               "value": "Admin"
         }
      ]
   }
   ```
  
  - **Saída de exemplo (PATCH)** 
    
   ```
   "Operations": [
   {
   "op": "Add",
   "path": "roles",
   "value": [
   {
   "value": "{\"id\":\"06b07648-ecfe-589f-9d2f-6325724a46ee\",\"value\":\"25\",\"displayName\":\"Role1234\"}"
   }
   ]
   ```  
O formato de pedido no PATCH e POST difere. Para garantir que o POST e o PATCH são enviados no mesmo formato, pode utilizar a bandeira de [características aqui](./application-provisioning-config-problem-scim-compatibility.md#flags-to-alter-the-scim-behavior)descrita. 

- **AppRoleAssignmentsComplex** 
  - **Quando usar:** Utilize a expressão AppRoleAssignmentsComplex para obter múltiplas funções para um utilizador. 
  - **Como configurar:** Editar a lista de atributos suportados como descrito acima para incluir um novo atributo para funções: 
  
    ![Adicionar funções](./media/customize-application-attributes/add-roles.png)<br>

    Em seguida, utilize a expressão AppRoleAssignmentsComplex para mapear o atributo de função personalizada, tal como mostrado na imagem abaixo:

    ![Adicionar AppRoleAssignmentsComplex](./media/customize-application-attributes/edit-attribute-approleassignmentscomplex.png)<br>
  - **Coisas a considerar**
    - Todas as funções serão avisionadas como primárias = falsas.
    - O POST contém o tipo de função. O pedido PATCH não contém o tipo. Estamos a trabalhar no envio do tipo nos pedidos POST e PATCH.
    
  - **Saída de exemplo** 
  
   ```json
   {
       "schemas": [
           "urn:ietf:params:scim:schemas:core:2.0:User"
      ],
      "externalId": "alias",
      "userName": "alias@contoso.OnMicrosoft.com",
      "active": true,
      "displayName": "First Name Last Name",
      "meta": {
           "resourceType": "User"
      },
      "roles": [
         {
               "primary": false,
               "type": "WindowsAzureActiveDirectoryRole",
               "display": "Admin",
               "value": "Admin"
         },
         {
               "primary": false,
               "type": "WindowsAzureActiveDirectoryRole",
               "display": "User",
             "value": "User"
         }
      ]
   }
   ```

  


## <a name="provisioning-a-multi-value-attribute"></a>Provisionamento de um atributo multi-valor
Certos atributos, como telefoneS, números e e-mails são atributos de vário valor onde poderá ser necessário especificar diferentes tipos de números de telefone ou e-mails. Utilize a expressão abaixo para atributos de vários valores. Permite especificar o tipo de atributo e mapear que para o atributo do utilizador Azure AD correspondente para o valor. 

* phoneNumbers[type eq "work"].value
* phoneNumbers[type eq "mobile"].value
* números de telefone[tipo eq "fax"].valor

   ```json
   "phoneNumbers": [
       {
         "value": "555-555-5555",
         "type": "work"
      },
      {
         "value": "555-555-5555",
         "type": "mobile"
      },
      {
         "value": "555-555-5555",
         "type": "fax"
      }
   ]
   ```

## <a name="restoring-the-default-attributes-and-attribute-mappings"></a>Restaurar os atributos padrão e atribuir mapeamentos

Se precisar de recomeçar e redefinir os mapeamentos existentes de volta ao seu estado predefinido, pode selecionar a caixa **de verificação de mapeamentos predefinidos restaurar** e guardar a configuração. Ao fazê-lo, define todos os mapeamentos e filtros de deteção como se a aplicação tivesse sido adicionada ao seu inquilino AZure AD da galeria de aplicações.

A seleção desta opção forçará efetivamente a ressincronização de todos os utilizadores enquanto o serviço de fornecimento estiver em execução.

> [!IMPORTANT]
> Recomendamos vivamente que **o estatuto de Provisioning** seja definido para **Off** antes de invocar esta opção.

## <a name="what-you-should-know"></a>O que deve saber

- O Microsoft Azure AD fornece uma implementação eficiente de um processo de sincronização. Num ambiente inicializado, apenas os objetos que necessitam de atualizações são processados durante um ciclo de sincronização.
- A atualização dos mapeamentos de atributos tem um impacto no desempenho de um ciclo de sincronização. Uma atualização da configuração de mapeamento de atributos requer que todos os objetos geridos sejam reavaliados.
- Uma boa prática recomendada é manter no mínimo o número de alterações consecutivas nos seus mapeamentos de atributos.
- A adição de um atributo fotográfico a ser aprovisionado numa aplicação não é suportado hoje em dia, uma vez que não é possível especificar o formato para sincronizar a fotografia. Pode solicitar a funcionalidade no [User Voice](https://feedback.azure.com/forums/169401-azure-active-directory)
- O atributo IsSoftDeleted é frequentemente parte dos mapeamentos padrão para uma aplicação. O IsSoftdeleted pode ser verdade num dos quatro cenários (o utilizador está fora de alcance devido a não ter sido atribuído à aplicação, o utilizador está fora de alcance por não ter atendedo a um filtro de escotagem, o utilizador foi apagado suavemente em AD Azure, ou a propriedade AccountEnabled é definida como falsa no utilizador). Não é aconselhável remover o atributo IsSoftDeleted dos mapeamentos do seu atributo.
- O serviço de prestação de AD Azure não suporta a prestação de valores nulos.
- A chave primária, tipicamente "ID", não deve ser incluída como um atributo-alvo nos mapeamentos do seu atributo. 
- O atributo de função normalmente precisa ser mapeado usando uma expressão, em vez de um mapeamento direto. Consulte a secção acima para obter mais detalhes sobre o mapeamento de funções. 
- Embora possa desativar grupos dos seus mapeamentos, os utilizadores incapacitados não são suportados. 

## <a name="next-steps"></a>Passos seguintes

- [Automatizar o Provisionamento/Desprovisionamento do Utilizador para aplicações SaaS](user-provisioning.md)
- [Expressãos de escrita para atributos-mapeamentos](functions-for-customizing-application-data.md)
- [Filtros de deteção para o provisionamento do utilizador](define-conditional-rules-for-provisioning-user-accounts.md)
- [Utilizar o SCIM para ativar o aprovisionamento automático de utilizadores e grupos do Azure Active Directory a aplicações](use-scim-to-provision-users-and-groups.md)
- [Lista de Tutoriais sobre Como Integrar Aplicações SaaS](../saas-apps/tutorial-list.md)