---
title: Personalizando mapeamentos de atributo do Azure AD | Microsoft Docs
description: Saiba quais mapeamentos de atributo para aplicativos SaaS no Azure Active Directory são como você pode modificá-los para atender às suas necessidades de negócios.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: cf1515bcf2223ae730a47f7105d51206ba638cd7
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77161618"
---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>Personalizando mapeamentos de atributo de provisionamento de usuário para aplicativos SaaS no Azure Active Directory

Microsoft Azure AD fornece suporte para provisionamento de usuário para aplicativos SaaS de terceiros, como Salesforce, G Suite e outros. Se você habilitar o provisionamento de usuário para um aplicativo SaaS de terceiros, o portal do Azure controlará seus valores de atributo por meio de mapeamentos de atributo.

Há um conjunto pré-configurado de atributos e mapeamentos de atributo entre objetos de usuário do Azure AD e os objetos de usuário de cada aplicativo SaaS. Algumas aplicações gerem outros tipos de objetos juntamente com os Utilizadores, como grupos.

Pode personalizar os mapeamentos de atributos padrão de acordo com as suas necessidades de negócio. Assim, pode alterar ou eliminar os mapeamentos de atributos existentes, ou criar novos mapeamentos de atributos.

## <a name="editing-user-attribute-mappings"></a>Editando atributos de usuário-mapeamentos

Siga estes passos para aceder à funcionalidade **Mapeamentos** de fornecimento de utilizadores:

1. Inscreva-se no [portal de Diretório Ativo Azure.](https://aad.portal.azure.com)
1. Selecione **aplicações Enterprise** a partir do painel esquerdo. É apresentada uma lista de todas as aplicações configuradas, incluindo aplicações que foram adicionadas a partir da galeria.
1. Selecione qualquer aplicativo para carregar seu painel de gerenciamento de aplicativo, no qual você pode exibir relatórios e gerenciar configurações de aplicativo.
1. Selecione **Provisioning** para gerir as definições de provisionamento da conta de utilizador para a aplicação selecionada.
1. Expandir **Mapeamentos** para visualizar e editar os atributos do utilizador que fluem entre a AD Azure e a aplicação-alvo. Se o aplicativo de destino oferecer suporte a ele, esta seção permitirá que você configure o provisionamento de grupos e contas de usuário opcionalmente.

   ![Usar mapeamentos para exibir e editar atributos de usuário](./media/customize-application-attributes/21.png)

1. Selecione uma configuração **de Mapeamento** para abrir o ecrã de Mapeamento do **Atributo** relacionado. Alguns mapeamentos de atributo são exigidos por um aplicativo SaaS para funcionar corretamente. Para atributos necessários, a funcionalidade **Eliminar** não está disponível.

   ![Usar mapeamento de atributo para configurar mapeamentos de atributo para aplicativos](./media/customize-application-attributes/22.png)

   Nesta imagem, pode ver que o atributo do **Username** de um objeto gerido no Salesforce é povoado com o valor do **userPrincipalName** do Objeto de Diretório Ativo Azure ligado.

1. Selecione um **Mapeamento** de Atributo existente para abrir o ecrã do **Atributo editar.** Aqui você pode editar os atributos de usuário que fluem entre o Azure AD e o aplicativo de destino.

   ![Usar o atributo editar para editar atributos de usuário](./media/customize-application-attributes/23.png)

### <a name="understanding-attribute-mapping-types"></a>Noções básicas sobre tipos de mapeamento de atributo

Com os mapeamentos de atributos, você controla como os atributos são populados em um aplicativo SaaS de terceiros.
Há quatro tipos diferentes de mapeamento com suporte:

- **Direto** – o atributo-alvo é povoado com o valor de um atributo do objeto ligado em Azure AD.
- **Constante** – o atributo-alvo é povoado com uma corda específica especificada.
- **Expressão** - o atributo-alvo é povoado com base no resultado de uma expressão semelhante a um script.
  Para mais informações, consulte [Expressões de Escrita para Atribuições em Diretório Ativo Azure](../app-provisioning/functions-for-customizing-application-data.md).
- **Nenhum** - o atributo alvo é deixado inalterado. No entanto, se o atributo de destino já estiver vazio, ele será preenchido com o valor padrão que você especificar.

Juntamente com estes quatro tipos básicos, os mapeamentos personalizados suportam o conceito de uma atribuição opcional de valor **por defeito.** A atribuição de valor padrão garante que um atributo de destino seja preenchido com um valor se não houver um valor no Azure AD ou no objeto de destino. A configuração mais comum é deixar em branco.

### <a name="understanding-attribute-mapping-properties"></a>Noções básicas sobre propriedades de mapeamento de atributo

Na seção anterior, você já foi apresentado à propriedade tipo de mapeamento de atributo.
Junto com essa propriedade, os mapeamentos de atributo também oferecem suporte aos seguintes atributos:

- **Atributo de origem** - O atributo do utilizador do sistema de origem (exemplo: Diretório Ativo Azure).
- **Atributo-alvo** – O atributo do utilizador no sistema-alvo (exemplo: ServiceNow).
- **Valor predefinido se nulo (opcional)** - O valor que será passado para o sistema-alvo se o atributo de origem for nulo. Esse valor só será provisionado quando um usuário for criado. O "valor padrão quando nulo" não será provisionado durante a atualização de um usuário existente. Se, por exemplo, pretender fornecer todos os utilizadores existentes no sistema-alvo com um determinado Título de Trabalho (quando for nulo no sistema de origem), pode utilizar a seguinte [expressão](../app-provisioning/functions-for-customizing-application-data.md): Switch(IsPresent([jobTitle]), "DefaultValue", "True", [jobTitle]). Certifique-se de substituir o "valor padrão" pelo que você gostaria de provisionar quando nulo no sistema de origem. 
- **Combine objetos usando este atributo** – Se este mapeamento deve ser usado para identificar exclusivamente os utilizadores entre a fonte e os sistemas de destino. Normalmente, ele é definido no atributo userPrincipalName ou mail no Azure AD, que normalmente é mapeado para um campo de nome de usuário em um aplicativo de destino.
- **Precedência correspondente** – Podem ser definidos múltiplos atributos correspondentes. Quando há vários, eles são avaliados na ordem definida por esse campo. Assim que um jogo é encontrado, não são avaliados mais atributos correspondentes.
- **Aplique este mapeamento**
  - **Sempre** – Aplique este mapeamento tanto na criação do utilizador como nas ações de atualização.
  - **Apenas durante** a criação - Aplique este mapeamento apenas em ações de criação do utilizador.

## <a name="matching-users-in-the-source-and-target--systems"></a>Usuários correspondentes nos sistemas de origem e de destino
O serviço de provisionamento do Azure AD pode ser implantado em cenários "greenfield" (onde os usuários não saem no sistema de destino) e nos cenários "Brownfield" (onde os usuários já existem no sistema de destino). Para dar suporte a ambos os cenários, o serviço de provisionamento usa o conceito de atributos correspondentes. Os atributos correspondentes permitem que você determine como identificar exclusivamente um usuário na origem e corresponder ao usuário no destino. Como parte do planejamento de sua implantação, identifique o atributo que pode ser usado para identificar exclusivamente um usuário nos sistemas de origem e de destino. Itens a serem observados:

- **Os atributos correspondentes devem ser únicos:** Os clientes usam frequentemente atributos como userPrincipalName, mail ou object ID como o atributo correspondente.
- **Vários atributos podem ser usados como atributos correspondentes:** Pode definir múltiplos atributos a avaliar quando combinar utilizadores e a ordem em que são avaliados (definidos como precedência correspondente na UI). Se, por exemplo, você definir três atributos como atributos correspondentes e um usuário for correspondido com exclusividade depois de avaliar os dois primeiros atributos, o serviço não avaliará o terceiro atributo. O serviço avaliará os atributos correspondentes na ordem especificada e interromperá a avaliação quando uma correspondência for encontrada.  
- O valor na fonte e no alvo não têm de **corresponder exatamente:** O valor no alvo pode ser uma função simples do valor na fonte. Portanto, um pode ter um atributo emailAddress na origem e o userPrincipalName no destino e corresponder por uma função do atributo emailAddress que substitui alguns caracteres por um valor constante.  
- **A correspondência com base numa combinação de atributos não é suportada:** A maioria das aplicações não suporta consulta com base em duas propriedades. Portanto, não é possível fazer a correspondência com base em uma combinação de atributos. É possível avaliar as propriedades únicas após a outra.
- **Todos os utilizadores devem ter um valor para pelo menos um atributo correspondente:** Se definir um atributo correspondente, todos os utilizadores devem ter um valor para esse atributo no sistema fonte. Se, por exemplo, você definir userPrincipalName como o atributo correspondente, todos os usuários deverão ter um userPrincipalName. Se você definir vários atributos correspondentes (por exemplo, extensionAttribute1 e email), nem todos os usuários precisarão ter o mesmo atributo correspondente. Um usuário poderia ter um extensionAttribute1, mas não um email, enquanto outro usuário poderia ter um email, mas sem extensionAttribute1. 
- **A aplicação-alvo deve apoiar a filtragem do atributo correspondente:** Os desenvolvedores de aplicações permitem a filtragem para um subconjunto de atributos no seu utilizador ou grupo API. Para aplicativos na Galeria, garantimos que o mapeamento de atributo padrão seja para um atributo no qual a API do aplicativo de destino dá suporte à filtragem. Ao alterar o atributo de correspondência padrão para o aplicativo de destino, verifique a documentação da API de terceiros para garantir que o atributo possa ser filtrado.  

## <a name="editing-group-attribute-mappings"></a>Editando atributo de grupo-mapeamentos

Um número selecionado de aplicativos, como ServiceNow, Box e G Suite, dá suporte à capacidade de provisionar objetos de grupo e objetos de usuário. Os objetos de grupo podem conter Propriedades de grupo, como nomes de exibição e aliases de email, juntamente com membros do grupo.

![Exemplo mostra o ServiceNow com objetos de grupo e usuário provisionados](./media/customize-application-attributes/24.png)

O fornecimento do grupo pode ser opcionalmente ativado ou desativado selecionando o mapeamento do grupo em **Mapeamentos,** e definindo **Ativado** para a opção que deseja no ecrã de Mapeamento do **Atributo.**

Os atributos provisionados como parte dos objetos de grupo podem ser personalizados da mesma maneira que os objetos de usuário, descritos anteriormente. 

> [!TIP]
> O fornecimento de objetos de grupo (propriedades e membros) é um conceito distinto de [atribuir grupos](../manage-apps/assign-user-or-group-access-portal.md) a uma aplicação. É possível atribuir um grupo a um aplicativo, mas apenas provisionar os objetos de usuário contidos no grupo. O provisionamento de objetos de grupo completo não é necessário para usar grupos em atribuições.

## <a name="editing-the-list-of-supported-attributes"></a>Editando a lista de atributos com suporte

Os atributos de usuário com suporte para um determinado aplicativo são pré-configurados. A maioria das APIs de gerenciamento de usuário do aplicativo não oferece suporte à descoberta de esquema. Portanto, o serviço de provisionamento do Azure AD não é capaz de gerar dinamicamente a lista de atributos com suporte fazendo chamadas para o aplicativo.

No entanto, alguns aplicativos dão suporte a atributos personalizados e o serviço de provisionamento do Azure AD pode ler e gravar em atributos personalizados. Para introduzir as suas definições no portal Azure, selecione a caixa de verificação de **opções avançadas do Show** na parte inferior do ecrã **de Mapeamento** do Atributo e, em seguida, selecione a lista de **atributos editar para** a sua aplicação.

Os aplicativos e sistemas que dão suporte à personalização da lista de atributos incluem:

- Salesforce
- ServiceNow
- Workday
- Diretório Ativo Azure ([AD Graph API atributos padrão](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity) e extensões de diretório personalizados são suportados)
- Aplicativos que suportam [O SCIM 2.0,](https://tools.ietf.org/html/rfc7643)onde os atributos definidos no [esquema central](https://tools.ietf.org/html/rfc7643) precisam de ser adicionados

> [!NOTE]
> A edição da lista de atributos com suporte é recomendada apenas para administradores que personalizaram o esquema de seus aplicativos e sistemas e têm conhecimento inicial de como seus atributos personalizados foram definidos. Isso às vezes requer familiaridade com as APIs e as ferramentas de desenvolvedor fornecidas por um aplicativo ou sistema.

Ao editar a lista de atributos com suporte, as seguintes propriedades são fornecidas:

- **Nome** - O nome do sistema do atributo, tal como definido no esquema do objecto-alvo.
- **Tipo** - O tipo de dados que o atributo armazena, tal como definido no esquema do objecto-alvo, que pode ser um dos seguintes tipos:
  - *Binário* - Atributo contém dados binários.
  - *Boolean* - Atributo contém um valor verdadeiro ou falso.
  - *DataTime* - Atributo contém uma corda de data.
  - *Inteiro* - Atributo contém um inteiro.
  - *Referência* - O atributo contém um ID que refere um valor armazenado noutra tabela na aplicação-alvo.
  - *String* - Atributo contém uma cadeia de texto.
- **Chave Primária?** -Se o atributo é definido como um campo de chave primária no esquema do objeto de destino.
- **Necessário?** -Se o atributo deve ser preenchido no aplicativo ou sistema de destino.
- **Multi-valor?** -Se o atributo dá suporte a vários valores.
- **Caso exato?** -Se os valores de atributos são avaliados de maneira diferenciada de maiúsculas e minúsculas.
- **Expressão API** - Não utilize, a menos que seja instruído pela documentação para um conector de provisionamento específico (como o Dia de Trabalho).
- **Atributo** de objeto referenciado - Se for um atributo tipo de referência, então este menu permite selecionar a tabela e atribuir na aplicação-alvo que contém o valor associado ao atributo. Por exemplo, se você tiver um atributo chamado "Department" cujo valor armazenado faz referência a um objeto em uma tabela "departamentos" separada, selecione "Departments.Name". As tabelas de referência e os principais campos de IDENTIFICAção suportados para uma determinada aplicação estão pré-configurados e atualmente não podem ser editados usando o portal Azure, mas podem ser editados usando a [API graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-configure-with-custom-target-attributes).

#### <a name="provisioning-a-custom-extension-attribute-to-a-scim-compliant-application"></a>Provisionando um atributo de extensão personalizado para um aplicativo compatível com SCIM
A RFC SCIM define um esquema de usuário e grupo principal, permitindo também que as extensões do esquema atendam às necessidades do seu aplicativo. Para adicionar um atributo personalizado a um aplicativo SCIM:
   1. Inscreva-se no [portal de Diretório Ativo Azure,](https://aad.portal.azure.com)selecione **Aplicações Empresariais,** selecione a sua aplicação e, em seguida, selecione **Provisioning**.
   2. Em **Mapeamentos,** selecione o objeto (utilizador ou grupo) para o qual deseja adicionar um atributo personalizado.
   3. Na parte inferior da página, selecione **Mostrar opções avançadas**.
   4. **Selecione editar lista de atributos para AppName**.
   5. Na parte inferior da lista de atributos, insira informações sobre o atributo personalizado nos campos fornecidos. Em seguida, selecione **Adicionar Atributo**.

Para aplicativos SCIM, o nome do atributo deve seguir o padrão mostrado no exemplo abaixo. O "CustomExtensionName" e "CustomAttribute" podem ser personalizados de acordo com os requisitos da sua aplicação, por exemplo: urn:ietf:params:scim:schemas:extension:extensionName:CustomAttribute or urn:ietf:params:scim:schemas:extension: Nome de extensão personalizada:2.0:Utilizador.CustomAttributeName:value

Essas instruções só são aplicáveis a aplicativos habilitados para SCIM. Aplicativos como ServiceNow e Salesforce não são integrados ao Azure AD usando SCIM e, portanto, não exigem esse namespace específico ao adicionar um atributo personalizado.

Atributos personalizados não podem ser atributos de referência ou atributos de valores múltiplos. Os atributos de extensão de vários valores personalizados atualmente têm suporte apenas para aplicativos na galeria.  
 
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


## <a name="provisioning-a-role-to-a-scim-app"></a>Provisionando uma função para um aplicativo SCIM
Use as etapas abaixo para provisionar funções para um usuário para seu aplicativo. Observe que a descrição abaixo é específica para aplicativos SCIM personalizados. Para aplicativos de galeria, como Salesforce e ServiceNow, use os mapeamentos de função predefinidos. Os marcadores abaixo descrevem como transformar o atributo AppRoleAssignments no formato esperado pelo aplicativo.

- Mapear uma appRoleAssignment em Azure AD para um papel na sua aplicação requer que transforme o atributo usando uma [expressão](../app-provisioning/functions-for-customizing-application-data.md). O atributo da appRoleAssignment **não deve ser mapeado diretamente** para um atributo de função sem usar uma expressão para analisar os detalhes do papel. 

- **Atribuição singleAppRole** 
  - **Quando utilizar:** Utilize a expressão SingleAppRoleAssignment para fornecer uma única função para um utilizador e especificar a função principal. 
  - **Como configurar:** Utilize os passos acima descritos para navegar na página de mapeamento de atributos e use a expressão SingleAppRoleAssignment para mapear as funções atributos. Há três atributos de papel a escolher: (papéis [primárioe "True"].display, roles[primary eq "True].type, e papéis[primário eq "True"].value). Você pode optar por incluir qualquer um dos atributos de função ou todos eles em seus mapeamentos. Se você quiser incluir mais de um, basta adicionar um novo mapeamento e incluí-lo como o atributo de destino.  
  
  ![Adicionar SingleAppRoleAssignment](./media/customize-application-attributes/edit-attribute-singleapproleassignment.png)
  - **Coisas a considerar**
    - Certifique-se de que várias funções não sejam atribuídas a um usuário. Não podemos garantir qual função será provisionada.
    
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
  
    ![Adicionar papéis](./media/customize-application-attributes/add-roles.png)<br>

    Em seguida, use a expressão AppRoleAssignmentsComplex para mapear para o atributo de função personalizada, conforme mostrado na imagem abaixo:

    ![Adicionar AppRoleAssignmentsComplex](./media/customize-application-attributes/edit-attribute-approleassignmentscomplex.png)<br>
  - **Coisas a considerar**
    - Todas as funções serão provisionadas como primárias = falso.
    - A POSTAgem contém o tipo de função. A solicitação de PATCH não contém o tipo. Estamos trabalhando para enviar o tipo em solicitações POST e PATCH.
    
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

  


## <a name="provisioning-a-multi-value-attribute"></a>Provisionando um atributo com vários valores
Determinados atributos, como phoneNumbers e emails, são atributos de vários valores, em que talvez seja necessário especificar diferentes tipos de números de telefone ou emails. Use a expressão abaixo para atributos de valores múltiplos. Ele permite que você especifique o tipo de atributo e mapeie-o para o atributo de usuário correspondente do Azure AD para o valor. 

* . Value de phoneNumbers [tipo eq "trabalho"]
* . Value de phoneNumbers [tipo eq "móvel"]
* . Value de phoneNumbers [tipo eq "fax"]

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

## <a name="restoring-the-default-attributes-and-attribute-mappings"></a>Restaurando atributos padrão e mapeamentos de atributo

Caso necessite de recomeçar e redefinir os mapeamentos existentes de volta ao seu estado padrão, pode selecionar a caixa de verificação de **mapeamentopadrão de restauro** e guardar a configuração. Isso define todos os mapeamentos como se o aplicativo acabou de ser adicionado ao seu locatário do Azure AD por meio da Galeria de aplicativos.

A seleção dessa opção forçará efetivamente uma ressincronização de todos os usuários enquanto o serviço de provisionamento estiver em execução.

> [!IMPORTANT]
> Recomendamos vivamente que o estado de **provisionamento** seja definido **antes** de invocar esta opção.

## <a name="what-you-should-know"></a>O que deve saber

- Microsoft Azure AD fornece uma implementação eficiente de um processo de sincronização. Em um ambiente inicializado, somente objetos que exigem atualizações são processados durante um ciclo de sincronização.
- A atualização de mapeamentos de atributo tem um impacto no desempenho de um ciclo de sincronização. Uma atualização para a configuração de mapeamento de atributo requer que todos os objetos gerenciados sejam reavaliados.
- Uma prática recomendada é manter o número de alterações consecutivas em seus mapeamentos de atributo no mínimo.
- Não há suporte para a adição de um atributo de foto a ser provisionado para um aplicativo hoje, pois não é possível especificar o formato para sincronizar a foto. Pode solicitar a funcionalidade na Voz do [Utilizador](https://feedback.azure.com/forums/169401-azure-active-directory)
- O atributo IsSoftDeleted geralmente faz parte dos mapeamentos padrão para um aplicativo. IsSoftdeleted pode ser verdadeiro em um dos quatro cenários (o usuário está fora do escopo devido à falta de atribuição do aplicativo, o usuário está fora do escopo devido à não reunião de um filtro de escopo, o usuário foi excluído de maneira reversível no Azure AD ou a propriedade AccountEnabled está definida como false  no usuário). 
- O serviço de provisionamento do Azure AD não dá suporte ao provisionamento de valores nulos

## <a name="next-steps"></a>Passos seguintes

- [Automatizar o fornecimento/deprovisionamento de utilizadores para apps SaaS](user-provisioning.md)
- [Expressões de escrita para atributo-mapeamento](../app-provisioning/functions-for-customizing-application-data.md)
- [Filtros de deteção para fornecimento de utilizadores](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
- [Utilizar o SCIM para ativar o aprovisionamento automático de utilizadores e grupos do Azure Active Directory a aplicações](use-scim-to-provision-users-and-groups.md)
- [Lista de Tutoriais sobre Como Integrar Apps SaaS](../saas-apps/tutorial-list.md)
