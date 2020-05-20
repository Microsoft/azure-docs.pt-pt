---
title: Personalizar mapeamentos de atributos Azure AD [ Microsoft Docs
description: Saiba quais os mapeamentos de atributos para aplicações SaaS no Diretório Ativo Azure são como pode modificá-las para atender às necessidades do seu negócio.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: mimart
ms.openlocfilehash: 9697bc2abd147a501466ba134f96512fe15173c0
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83639053"
---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>Personalização de mapeamento de atributos para aplicações SaaS no Diretório Ativo Azure

O Microsoft Azure AD fornece suporte para o fornecimento de utilizadores a aplicações SaaS de terceiros, tais como Salesforce, G Suite e outros. Se ativar o fornecimento de utilizadores para uma aplicação SaaS de terceiros, o portal Azure controla os seus valores de atributo através de mapeamentos de atributos.

Há um conjunto pré-configurado de atributos e mapeamentos de atributos entre objetos de utilizador da AD Azure e os objetos de utilizador de cada aplicação SaaS. Algumas aplicações gerem outros tipos de objetos juntamente com os Utilizadores, como grupos.

Pode personalizar os mapeamentos de atributos padrão de acordo com as suas necessidades de negócio. Assim, pode alterar ou eliminar os mapeamentos de atributos existentes, ou criar novos mapeamentos de atributos.

## <a name="editing-user-attribute-mappings"></a>Editar mapeamento de atributos do utilizador

Siga estes passos para aceder à funcionalidade **Mapeamentos** de fornecimento de utilizadores:

1. Inscreva-se no [portal de Diretório Ativo Azure.](https://aad.portal.azure.com)
1. Selecione **aplicações Enterprise** a partir do painel esquerdo. É apresentada uma lista de todas as aplicações configuradas, incluindo aplicações que foram adicionadas a partir da galeria.
1. Selecione qualquer aplicação para carregar o painel de gestão de aplicações, onde possa ver relatórios e gerir as definições de aplicações.
1. Selecione **Provisioning** para gerir as definições de provisionamento da conta de utilizador para a aplicação selecionada.
1. Expandir **Mapeamentos** para visualizar e editar os atributos do utilizador que fluem entre a AD Azure e a aplicação-alvo. Se a aplicação-alvo a suportar, esta secção permite configurar opcionalmente o fornecimento de grupos e contas de utilizador.

   ![Utilizar Mapeamentos para visualizar e editar atributos do utilizador](./media/customize-application-attributes/21.png)

1. Selecione uma configuração **de Mapeamento** para abrir o ecrã de Mapeamento do **Atributo** relacionado. Alguns mapeamentos de atributos são exigidos por uma aplicação SaaS para funcionar corretamente. Para atributos necessários, a funcionalidade **Eliminar** não está disponível.

   ![Utilizar o Mapeamento do Atributo para configurar mapeamento de atributos para apps](./media/customize-application-attributes/22.png)

   Nesta imagem, pode ver que o atributo do **Username** de um objeto gerido no Salesforce é povoado com o valor do **userPrincipalName** do Objeto de Diretório Ativo Azure ligado.

1. Selecione um **Mapeamento** de Atributo existente para abrir o ecrã do **Atributo editar.** Aqui pode editar os atributos do utilizador que fluem entre a AD Azure e a aplicação-alvo.

   ![Utilizar o Atributo editar para editar atributos do utilizador](./media/customize-application-attributes/23.png)

### <a name="understanding-attribute-mapping-types"></a>Compreender tipos de mapeamento de atributos

Com mapeamentos de atributos, você controla como os atributos são povoados em uma aplicação SaaS de terceiros.
Existem quatro tipos diferentes de mapeamento suportados:

- **Direto** – o atributo-alvo é povoado com o valor de um atributo do objeto ligado em Azure AD.
- **Constante** – o atributo-alvo é povoado com uma corda específica especificada.
- **Expressão** - o atributo-alvo é povoado com base no resultado de uma expressão semelhante a um script.
  Para mais informações, consulte [Expressões de Escrita para Atribuições em Diretório Ativo Azure](../app-provisioning/functions-for-customizing-application-data.md).
- **Nenhum** - o atributo alvo é deixado inalterado. No entanto, se o atributo-alvo estiver sempre vazio, é povoado com o valor Padrão que especifica.

Juntamente com estes quatro tipos básicos, os mapeamentos personalizados suportam o conceito de uma atribuição opcional de valor **por defeito.** A atribuição de valor predefinido garante que um atributo-alvo é povoado com um valor se não houver um valor em Azure AD ou no objeto-alvo. A configuração mais comum é deixar isto em branco.

### <a name="understanding-attribute-mapping-properties"></a>Compreender propriedades de mapeamento de atributos

Na secção anterior, já foi introduzido na propriedade do tipo de mapeamento de atributos.
Juntamente com esta propriedade, os atributos-mapeamentos também suportam os seguintes atributos:

- **Atributo de origem** - O atributo do utilizador do sistema de origem (exemplo: Diretório Ativo Azure).
- **Atributo-alvo** – O atributo do utilizador no sistema-alvo (exemplo: ServiceNow).
- **Valor predefinido se nulo (opcional)** - O valor que será passado para o sistema-alvo se o atributo de origem for nulo. Este valor só será provisionado quando um utilizador for criado. O "valor predefinido quando nulo" não será provisionado ao atualizar um utilizador existente. Se, por exemplo, pretender fornecer todos os utilizadores existentes no sistema-alvo com um determinado Título de Trabalho (quando for nulo no sistema de origem), pode utilizar a seguinte [expressão](../app-provisioning/functions-for-customizing-application-data.md): Switch(IsPresent([jobTitle]), "DefaultValue", "True", [jobTitle]). Certifique-se de substituir o "Valor Padrão" pelo que gostaria de fornecer quando nula no sistema de origem. 
- **Combine objetos usando este atributo** – Se este mapeamento deve ser usado para identificar exclusivamente os utilizadores entre a fonte e os sistemas de destino. É normalmente definido no utilizadorPrincipalName ou atribuidor de correio em Azure AD, que é tipicamente mapeado para um campo de nome de utilizador numa aplicação alvo.
- **Precedência correspondente** – Podem ser definidos múltiplos atributos correspondentes. Quando há vários, são avaliados na ordem definida por este campo. Assim que um jogo é encontrado, não são avaliados mais atributos correspondentes. Embora possa definir quantos atributos correspondentes gostaria, considere se os atributos que está a usar como atributos correspondentes são verdadeiramente únicos e precisam de ser atributos correspondentes. Geralmente, os clientes têm 1 ou 2 atributos correspondentes na sua configuração. 
- **Aplique este mapeamento**
  - **Sempre** – Aplique este mapeamento tanto na criação do utilizador como nas ações de atualização.
  - **Apenas durante** a criação - Aplique este mapeamento apenas em ações de criação do utilizador.

## <a name="matching-users-in-the-source-and-target--systems"></a>Utilizadores correspondentes nos sistemas de origem e alvo
O serviço de provisionamento de AD Azure pode ser implementado em ambos os cenários de "greenfield" (onde os utilizadores não saem no sistema alvo) e cenários de "brownfield" (onde os utilizadores já existem no sistema alvo). Para suportar ambos os cenários, o serviço de provisionamento utiliza o conceito de atributos correspondentes. Os atributos correspondentes permitem determinar como identificar um utilizador de forma única na fonte e corresponder ao utilizador no alvo. Como parte do planeamento da sua implementação, identifique o atributo que pode ser usado para identificar de forma única um utilizador nos sistemas de origem e alvo. Coisas a notar:

- **Os atributos correspondentes devem ser únicos:** Os clientes usam frequentemente atributos como userPrincipalName, mail ou object ID como o atributo correspondente.
- **Vários atributos podem ser usados como atributos correspondentes:** Pode definir múltiplos atributos a avaliar quando combinar utilizadores e a ordem em que são avaliados (definidos como precedência correspondente na UI). Se, por exemplo, definir três atributos como atributos correspondentes, e um utilizador for exclusivamente igualado após avaliar os dois primeiros atributos, o serviço não avaliará o terceiro atributo. O serviço avaliará os atributos correspondentes na ordem especificada e deixará de avaliar quando for encontrada uma correspondência.  
- O valor na fonte e no alvo não têm de **corresponder exatamente:** O valor no alvo pode ser uma função simples do valor na fonte. Assim, pode-se ter um atributo de endereço de e-mail na fonte e no utilizadorPrincipalName no alvo, e corresponder por uma função do atributo do emailAddress que substitui alguns caracteres por algum valor constante.  
- **A correspondência com base numa combinação de atributos não é suportada:** A maioria das aplicações não suporta consulta com base em duas propriedades. Portanto, não é possível combinar com base numa combinação de atributos. É possível avaliar propriedades individuais após outra.
- **Todos os utilizadores devem ter um valor para pelo menos um atributo correspondente:** Se definir um atributo correspondente, todos os utilizadores devem ter um valor para esse atributo no sistema fonte. Se, por exemplo, definir o userPrincipalName como o atributo correspondente, todos os utilizadores devem ter um nome principal de utilizador. Se definir vários atributos correspondentes (por exemplo, extensãoAttribute1 e correio), nem todos os utilizadores têm de ter o mesmo atributo correspondente. Um utilizador pode ter uma extensãoAttribute1, mas não correio, enquanto outro utilizador poderia ter correio, mas nenhuma extensãoAttribute1. 
- **A aplicação-alvo deve apoiar a filtragem do atributo correspondente:** Os desenvolvedores de aplicações permitem a filtragem para um subconjunto de atributos no seu utilizador ou grupo API. Para aplicações na galeria, garantimos que o mapeamento de atributo padrão é para um atributo que a API da aplicação alvo suporta a filtragem. Ao alterar o atributo de correspondência predefinido para a aplicação-alvo, verifique a documentação da API de terceiros para garantir que o atributo pode ser filtrado.  

## <a name="editing-group-attribute-mappings"></a>Edição de atributos-mapeamentos de grupo

Um número selecionado de aplicações, tais como ServiceNow, Box e G Suite, suportam a capacidade de fornecer objetos do Grupo e objetos do Utilizador. Os objetos de grupo podem conter propriedades de grupo, tais como nomes de exibição e pseudónimos de e-mail, juntamente com membros do grupo.

![Exemplo mostra ServiceNow com objetos de grupo e utilizador provisionados](./media/customize-application-attributes/24.png)

O fornecimento do grupo pode ser opcionalmente ativado ou desativado selecionando o mapeamento do grupo em **Mapeamentos,** e definindo **Ativado** para a opção que deseja no ecrã de Mapeamento do **Atributo.**

Os atributos aprovisionados como parte de objetos do Grupo podem ser personalizados da mesma forma que os objetos do Utilizador, descritos anteriormente. 

> [!TIP]
> O fornecimento de objetos de grupo (propriedades e membros) é um conceito distinto de [atribuir grupos](../manage-apps/assign-user-or-group-access-portal.md) a uma aplicação. É possível atribuir um grupo a uma aplicação, mas apenas fornecer os objetos de utilizador contidos no grupo. O fornecimento de objetos de grupo completos não é obrigado a utilizar grupos em atribuições.

## <a name="editing-the-list-of-supported-attributes"></a>Editar a lista de atributos suportados

Os atributos do utilizador suportados para uma determinada aplicação são pré-configurados. A maioria das APIs de gestão de utilizadores da aplicação não suporta a descoberta de esquemas. Assim, o serviço de provisionamento de AD Azure não é capaz de gerar dinamicamente a lista de atributos suportados fazendo chamadas para a aplicação.

No entanto, algumas aplicações suportam atributos personalizados, e o serviço de provisionamento de AD Azure pode ler e escrever a tributos personalizados. Para introduzir as suas definições no portal Azure, selecione a caixa de verificação de **opções avançadas do Show** na parte inferior do ecrã **de Mapeamento** do Atributo e, em seguida, selecione a lista de **atributos editar para** a sua aplicação.

Aplicações e sistemas que suportam a personalização da lista de atributos incluem:

- Salesforce
- ServiceNow
- Workday
- Diretório Ativo Azure ([AD Graph API atributos padrão](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity) e extensões de diretório personalizados são suportados)
- Aplicativos que suportam [O SCIM 2.0,](https://tools.ietf.org/html/rfc7643)onde os atributos definidos no [esquema central](https://tools.ietf.org/html/rfc7643) precisam de ser adicionados

> [!NOTE]
> A edição da lista de atributos suportados só é recomendada para administradores que personalizaram o esquema das suas aplicações e sistemas, e têm conhecimento em primeira mão de como os seus atributos personalizados foram definidos. Isto requer, por vezes, familiaridade com as APIs e ferramentas de desenvolvimento fornecidas por uma aplicação ou sistema.

Ao editar a lista de atributos suportados, são fornecidas as seguintes propriedades:

- **Nome** - O nome do sistema do atributo, tal como definido no esquema do objecto-alvo.
- **Tipo** - O tipo de dados que o atributo armazena, tal como definido no esquema do objecto-alvo, que pode ser um dos seguintes tipos:
  - *Binário* - Atributo contém dados binários.
  - *Boolean* - Atributo contém um valor verdadeiro ou falso.
  - *DataTime* - Atributo contém uma corda de data.
  - *Inteiro* - Atributo contém um inteiro.
  - *Referência* - O atributo contém um ID que refere um valor armazenado noutra tabela na aplicação-alvo.
  - *String* - Atributo contém uma cadeia de texto.
- **Chave Primária?** - Se o atributo é definido como um campo-chave primário no esquema do objeto alvo.
- **Necessário?** - Se o atributo é obrigado a ser povoado na aplicação ou sistema-alvo.
- **Multi-valor?** - Se o atributo suporta vários valores.
- **Caso exato?** - Se os valores dos atributos são avaliados de forma sensível aos casos.
- **Expressão API** - Não utilize, a menos que seja instruído pela documentação para um conector de provisionamento específico (como o Dia de Trabalho).
- **Atributo** de objeto referenciado - Se for um atributo tipo de referência, então este menu permite selecionar a tabela e atribuir na aplicação-alvo que contém o valor associado ao atributo. Por exemplo, se tiver um atributo chamado "Departamento" cujo valor armazenado referencia um objeto numa tabela separada de "Departamentos", selecionaria "Departments.Name". As tabelas de referência e os principais campos de ID suportados para uma determinada aplicação estão pré-configurados e atualmente não podem ser editados usando o portal Azure, mas podem ser editados usando a [API do Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-configure-with-custom-target-attributes).

#### <a name="provisioning-a-custom-extension-attribute-to-a-scim-compliant-application"></a>Fornecimento de um atributo de extensão personalizado a uma aplicação compatível com o SCIM
O SCIM RFC define um esquema de utilizador e grupo principal, ao mesmo tempo que permite que as extensões ao esquema satisfaçam as necessidades da sua aplicação. Para adicionar um atributo personalizado a uma aplicação SCIM:
   1. Inscreva-se no [portal de Diretório Ativo Azure,](https://aad.portal.azure.com)selecione **Aplicações Empresariais,** selecione a sua aplicação e, em seguida, selecione **Provisioning**.
   2. Em **Mapeamentos,** selecione o objeto (utilizador ou grupo) para o qual deseja adicionar um atributo personalizado.
   3. Na parte inferior da página, selecione **Mostrar opções avançadas**.
   4. **Selecione editar lista de atributos para AppName**.
   5. Na parte inferior da lista de atributos, introduza informações sobre o atributo personalizado nos campos fornecidos. Em seguida, selecione **Adicionar Atributo**.

Para aplicações SCIM, o nome do atributo deve seguir o padrão indicado no exemplo abaixo. O "CustomExtensionName" e "CustomAttribute" podem ser personalizados de acordo com os requisitos da sua aplicação, por exemplo:  
 * urn:ietf:params:scim:schemas:extensionName:2.0:User:CustomAttribute 
 * urn:ietf:params:scim:schemas:extensão:2.0:CustomExtensionName:CustomAttribute  
 * urn:ietf:params:scim:schemas:extensionName:2.0:User.CustomAttributeName:value

Estas instruções aplicam-se apenas às aplicações ativadas pelo SCIM. Aplicações como ServiceNow e Salesforce não estão integradas com a Azure AD utilizando o SCIM, pelo que não requerem este espaço de nome específico ao adicionar um atributo personalizado.

Os atributos personalizados não podem ser atributos referenciais ou atributos de vários valores. Atualmente, os atributos personalizados de extensão de vários valores são suportados apenas para aplicações na galeria.  
 
**Representação de exemplo de um utilizador com um atributo de extensão:**

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
     "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:CustomAttribute:User": {
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


## <a name="provisioning-a-role-to-a-scim-app"></a>Prestação de um papel a uma aplicação SCIM
Utilize os passos abaixo para fornecer funções para um utilizador na sua aplicação. Note que a descrição abaixo é específica para aplicações SCIM personalizadas. Para aplicações de galerias como Salesforce e ServiceNow, utilize os mapeamentos de papéis pré-definidos. As balas abaixo descrevem como transformar as Atribuições de AppRoleatribuis ao formato que a sua aplicação espera.

- Mapear uma appRoleAssignment em Azure AD para um papel na sua aplicação requer que transforme o atributo usando uma [expressão](../app-provisioning/functions-for-customizing-application-data.md). O atributo da appRoleAssignment **não deve ser mapeado diretamente** para um atributo de função sem usar uma expressão para analisar os detalhes do papel. 

- **Atribuição singleAppRole** 
  - **Quando utilizar:** Utilize a expressão SingleAppRoleAssignment para fornecer uma única função para um utilizador e especificar a função principal. 
  - **Como configurar:** Utilize os passos acima descritos para navegar na página de mapeamento de atributos e use a expressão SingleAppRoleAssignment para mapear as funções atributos. Há três atributos de papel a escolher: (papéis [primárioe "True"].display, roles[primary eq "True].type, e papéis[primário eq "True"].value). Pode optar por incluir todos ou todos os atributos de papel nos seus mapeamentos. Se quiser incluir mais do que um, basta adicionar um novo mapeamento e incluí-lo como o atributo-alvo.  
  
  ![Adicionar atribuição de funções singleapprole](./media/customize-application-attributes/edit-attribute-singleapproleassignment.png)
  - **Coisas a considerar**
    - Certifique-se de que várias funções não são atribuídas a um utilizador. Não podemos garantir qual será o papel que será aprovisionado.
    
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
               "primary": true,
               "type": "WindowsAzureActiveDirectoryRole",
               "value": "Admin"
         }
      ]
   }
   ```
  
- **AppRoleAssignmentsComplex** 
  - **Quando utilizar:** Utilize a expressão AppRoleAssignmentsComplex para fornecer múltiplas funções para um utilizador. 
  - **Como configurar:** Editar a lista de atributos suportados acima descritos para incluir um novo atributo para funções: 
  
    ![Adicionar funções](./media/customize-application-attributes/add-roles.png)<br>

    Em seguida, utilize a expressão AppRoleAssignmentsComplex para mapear o atributo de função personalizada, como mostrado na imagem abaixo:

    ![Adicionar AppRoleAssignmentsComplex](./media/customize-application-attributes/edit-attribute-approleassignmentscomplex.png)<br>
  - **Coisas a considerar**
    - Todas as funções serão provisadas como primárias = falsas.
    - O POST contém o tipo de função. O pedido patch não contém tipo. Estamos a trabalhar no envio do tipo tanto nos pedidos post como patch.
    
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

  


## <a name="provisioning-a-multi-value-attribute"></a>Fornecimento de um atributo de vários valores
Certos atributos, como números de telefone e e-mails, são atributos de vários valores onde poderá ser necessário especificar diferentes tipos de números de telefone ou e-mails. Utilize a expressão abaixo para atributos de vários valores. Permite especificar o tipo de atributo e mapear isso ao atributo correspondente do utilizador Azure AD pelo valor. 

* telefoneNumbers[tipo eq "trabalho"].valor
* telefoneNumbers[tipo eq "mobile"].valor
* telefoneNumbers[tipo eq "fax"].valor

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

## <a name="restoring-the-default-attributes-and-attribute-mappings"></a>Restaurar os atributos padrão e atributo-mapeamento

Caso necessite de recomeçar e redefinir os mapeamentos existentes de volta ao seu estado padrão, pode selecionar a caixa de verificação de **mapeamentopadrão de restauro** e guardar a configuração. Ao fazê-lo, todos os mapeamentos e filtros de deteção como se a aplicação tivesse sido adicionada ao seu inquilino Azure AD a partir da galeria de aplicações.

A seleção desta opção forçará efetivamente uma ressincronização de todos os utilizadores enquanto o serviço de provisionamento estiver em funcionamento.

> [!IMPORTANT]
> Recomendamos vivamente que o estado de **provisionamento** seja definido **antes** de invocar esta opção.

## <a name="what-you-should-know"></a>O que deve saber

- A Microsoft Azure AD fornece uma implementação eficiente de um processo de sincronização. Num ambiente inicializado, apenas os objetos que requerem atualizações são processados durante um ciclo de sincronização.
- Atualizar os mapeamentos de atributos tem um impacto no desempenho de um ciclo de sincronização. Uma atualização da configuração de mapeamento de atributos requer que todos os objetos geridos sejam reavaliados.
- Uma boa prática recomendada é manter o número de alterações consecutivas nos seus mapeamentos de atributos no mínimo.
- A adição de um atributo fotográfico a fornecer a uma aplicação não é suportada hoje, uma vez que não pode especificar o formato para sincronizar a fotografia. Pode solicitar a funcionalidade na Voz do [Utilizador](https://feedback.azure.com/forums/169401-azure-active-directory)
- O atributo IsSoftDeleted é frequentemente parte dos mapeamentos padrão para uma aplicação. IsSoftdeleted pode ser verdade num dos quatro cenários (o utilizador está fora de âmbito devido ao facto de não ter sido atribuído da aplicação, o utilizador está fora de âmbito devido ao não cumprimento de um filtro de deteção, o utilizador foi eliminado suavemente em Azure AD, ou a conta De propriedadeEnabled está definida como falsa no utilizador). Não é aconselhável remover o atributo IsSoftDeleted dos seus mapeamentos de atributos.
- O serviço de provisionamento da AD Azure não suporta o fornecimento de valores nulos.
- A chave primária, tipicamente "ID", não deve ser incluída como um atributo-alvo nos seus mapeamentos de atributos. 
- O atributo de papel normalmente precisa ser mapeado usando uma expressão, em vez de um mapeamento direto. Consulte a secção acima para mais detalhes sobre o mapeamento de papéis. 

## <a name="next-steps"></a>Passos seguintes

- [Automatizar o fornecimento/deprovisionamento de utilizadores para apps SaaS](user-provisioning.md)
- [Expressões de escrita para atributo-mapeamento](../app-provisioning/functions-for-customizing-application-data.md)
- [Filtros de deteção para fornecimento de utilizadores](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
- [Utilizar o SCIM para ativar o aprovisionamento automático de utilizadores e grupos do Azure Active Directory a aplicações](use-scim-to-provision-users-and-groups.md)
- [Lista de Tutoriais sobre Como Integrar Aplicações SaaS](../saas-apps/tutorial-list.md)
