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
ms.openlocfilehash: 804eb63406b33b94e70ef56e0066fa213be04708
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74997059"
---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>Personalizando mapeamentos de atributo de provisionamento de usuário para aplicativos SaaS no Azure Active Directory

Microsoft Azure AD fornece suporte para provisionamento de usuário para aplicativos SaaS de terceiros, como Salesforce, G Suite e outros. Se você habilitar o provisionamento de usuário para um aplicativo SaaS de terceiros, o portal do Azure controlará seus valores de atributo por meio de mapeamentos de atributo.

Há um conjunto pré-configurado de atributos e mapeamentos de atributo entre objetos de usuário do Azure AD e os objetos de usuário de cada aplicativo SaaS. Alguns aplicativos gerenciam outros tipos de objetos junto com os usuários, como grupos.

Você pode personalizar os mapeamentos de atributo padrão de acordo com suas necessidades de negócios. Portanto, você pode alterar ou excluir mapeamentos de atributo existentes ou criar novos mapeamentos de atributo.

## <a name="editing-user-attribute-mappings"></a>Editando atributos de usuário-mapeamentos

Siga estas etapas para acessar o recurso **mapeamentos** do provisionamento de usuário:

1. Entre no portal de [Azure Active Directory](https://aad.portal.azure.com).
1. Selecione **aplicativos empresariais** no painel esquerdo. Uma lista de todos os aplicativos configurados é mostrada, incluindo os aplicativos que foram adicionados da galeria.
1. Selecione qualquer aplicativo para carregar seu painel de gerenciamento de aplicativo, no qual você pode exibir relatórios e gerenciar configurações de aplicativo.
1. Selecione **provisionamento** para gerenciar as configurações de provisionamento de conta de usuário para o aplicativo selecionado.
1. Expanda **mapeamentos** para exibir e editar os atributos de usuário que fluem entre o Azure AD e o aplicativo de destino. Se o aplicativo de destino oferecer suporte a ele, esta seção permitirá que você configure o provisionamento de grupos e contas de usuário opcionalmente.

   ![Usar mapeamentos para exibir e editar atributos de usuário](./media/customize-application-attributes/21.png)

1. Selecione uma configuração de **mapeamentos** para abrir a tela de **mapeamento de atributo** relacionada. Alguns mapeamentos de atributo são exigidos por um aplicativo SaaS para funcionar corretamente. Para os atributos necessários, o recurso de **exclusão** não está disponível.

   ![Usar mapeamento de atributo para configurar mapeamentos de atributo para aplicativos](./media/customize-application-attributes/22.png)

   Nesta captura de tela, você pode ver que o atributo **username** de um objeto gerenciado no Salesforce é preenchido com o valor **userPrincipalName** do objeto Azure Active Directory vinculado.

1. Selecione um **mapeamento de atributo** existente para abrir a tela **Editar atributo** . Aqui você pode editar os atributos de usuário que fluem entre o Azure AD e o aplicativo de destino.

   ![Usar o atributo editar para editar atributos de usuário](./media/customize-application-attributes/23.png)

### <a name="understanding-attribute-mapping-types"></a>Noções básicas sobre tipos de mapeamento de atributo

Com os mapeamentos de atributos, você controla como os atributos são populados em um aplicativo SaaS de terceiros.
Há quatro tipos diferentes de mapeamento com suporte:

- **Direto** – o atributo de destino é populado com o valor de um atributo do objeto vinculado no Azure AD.
- **Constante** – o atributo de destino é populado com uma cadeia de caracteres específica que você especificou.
- **Expressão** -o atributo de destino é preenchido com base no resultado de uma expressão do tipo script.
  Para obter mais informações, consulte [escrevendo expressões para mapeamentos de atributo em Azure Active Directory](functions-for-customizing-application-data.md).
- **Nenhum** -o atributo de destino é deixado sem modificações. No entanto, se o atributo de destino já estiver vazio, ele será preenchido com o valor padrão que você especificar.

Juntamente com esses quatro tipos básicos, os mapeamentos de atributo personalizados dão suporte ao conceito de uma atribuição de valor **padrão** opcional. A atribuição de valor padrão garante que um atributo de destino seja preenchido com um valor se não houver um valor no Azure AD ou no objeto de destino. A configuração mais comum é deixar em branco.

### <a name="understanding-attribute-mapping-properties"></a>Noções básicas sobre propriedades de mapeamento de atributo

Na seção anterior, você já foi apresentado à propriedade tipo de mapeamento de atributo.
Junto com essa propriedade, os mapeamentos de atributo também oferecem suporte aos seguintes atributos:

- **Atributo de origem** -o atributo de usuário do sistema de origem (exemplo: Azure Active Directory).
- **Atributo de destino** – o atributo de usuário no sistema de destino (exemplo: ServiceNow).
- **Corresponder objetos usando este atributo** – se esse mapeamento deve ser usado para identificar exclusivamente os usuários entre os sistemas de origem e de destino. Normalmente, ele é definido no atributo userPrincipalName ou mail no Azure AD, que normalmente é mapeado para um campo de nome de usuário em um aplicativo de destino.
- **Precedência de correspondência** – vários atributos correspondentes podem ser definidos. Quando há vários, eles são avaliados na ordem definida por esse campo. Assim que uma correspondência for encontrada, nenhum atributo correspondente será avaliado.
- **Aplicar este mapeamento**
  - **Sempre** – aplique esse mapeamento nas ações de criação e atualização do usuário.
  - **Somente durante a criação** – aplique esse mapeamento somente em ações de criação de usuário.

## <a name="matching-users-in-the-source-and-target--systems"></a>Usuários correspondentes nos sistemas de origem e de destino
O serviço de provisionamento do Azure AD pode ser implantado em cenários "greenfield" (onde os usuários não saem no sistema de destino) e nos cenários "Brownfield" (onde os usuários já existem no sistema de destino). Para dar suporte a ambos os cenários, o serviço de provisionamento usa o conceito de atributos correspondentes. Os atributos correspondentes permitem que você determine como identificar exclusivamente um usuário na origem e corresponder ao usuário no destino. Como parte do planejamento de sua implantação, identifique o atributo que pode ser usado para identificar exclusivamente um usuário nos sistemas de origem e de destino. Itens a serem observados:

- Os **atributos correspondentes devem ser exclusivos:** Os clientes geralmente usam atributos como userPrincipalName, email ou ID de objeto como o atributo correspondente.
- **Vários atributos podem ser usados como atributos correspondentes:** Você pode definir vários atributos a serem avaliados quando os usuários correspondentes e a ordem em que eles são avaliados (definidos como precedência de correspondência na interface do usuário). Se, por exemplo, você definir três atributos como atributos correspondentes e um usuário for correspondido com exclusividade depois de avaliar os dois primeiros atributos, o serviço não avaliará o terceiro atributo. O serviço avaliará os atributos correspondentes na ordem especificada e interromperá a avaliação quando uma correspondência for encontrada.  
- **O valor na origem e no destino não precisam corresponder exatamente:** O valor no destino pode ser uma função simples do valor na origem. Portanto, um pode ter um atributo emailAddress na origem e o userPrincipalName no destino e corresponder por uma função do atributo emailAddress que substitui alguns caracteres por um valor constante.  
- **Não há suporte para a correspondência com base em uma combinação de atributos:** A maioria dos aplicativos não oferece suporte à consulta com base em duas propriedades. Portanto, não é possível fazer a correspondência com base em uma combinação de atributos. É possível avaliar as propriedades únicas após a outra.
- **Todos os usuários devem ter um valor para pelo menos um atributo correspondente:** Se você definir um atributo correspondente, todos os usuários deverão ter um valor para esse atributo no sistema de origem. Se, por exemplo, você definir userPrincipalName como o atributo correspondente, todos os usuários deverão ter um userPrincipalName. Se você definir vários atributos correspondentes (por exemplo, extensionAttribute1 e email), nem todos os usuários precisarão ter o mesmo atributo correspondente. Um usuário poderia ter um extensionAttribute1, mas não um email, enquanto outro usuário poderia ter um email, mas sem extensionAttribute1. 
- **O aplicativo de destino deve dar suporte à filtragem no atributo correspondente:** Os desenvolvedores de aplicativos permitem a filtragem de um subconjunto de atributos em sua API de usuário ou de grupo. Para aplicativos na Galeria, garantimos que o mapeamento de atributo padrão seja para um atributo no qual a API do aplicativo de destino dá suporte à filtragem. Ao alterar o atributo de correspondência padrão para o aplicativo de destino, verifique a documentação da API de terceiros para garantir que o atributo possa ser filtrado.  

## <a name="editing-group-attribute-mappings"></a>Editando atributo de grupo-mapeamentos

Um número selecionado de aplicativos, como ServiceNow, Box e G Suite, dá suporte à capacidade de provisionar objetos de grupo e objetos de usuário. Os objetos de grupo podem conter Propriedades de grupo, como nomes de exibição e aliases de email, juntamente com membros do grupo.

![Exemplo mostra o ServiceNow com objetos de grupo e usuário provisionados](./media/customize-application-attributes/24.png)

O provisionamento de grupo pode ser opcionalmente habilitado ou desabilitado selecionando o mapeamento de grupo em **mapeamentos**e a configuração **habilitada** para a opção desejada na tela **mapeamento de atributos** .

Os atributos provisionados como parte dos objetos de grupo podem ser personalizados da mesma maneira que os objetos de usuário, descritos anteriormente. 

> [!TIP]
> O provisionamento de objetos de grupo (Propriedades e membros) é um conceito distinto de [atribuir grupos](assign-user-or-group-access-portal.md) a um aplicativo. É possível atribuir um grupo a um aplicativo, mas apenas provisionar os objetos de usuário contidos no grupo. O provisionamento de objetos de grupo completo não é necessário para usar grupos em atribuições.

## <a name="editing-the-list-of-supported-attributes"></a>Editando a lista de atributos com suporte

Os atributos de usuário com suporte para um determinado aplicativo são pré-configurados. A maioria das APIs de gerenciamento de usuário do aplicativo não oferece suporte à descoberta de esquema. Portanto, o serviço de provisionamento do Azure AD não é capaz de gerar dinamicamente a lista de atributos com suporte fazendo chamadas para o aplicativo.

No entanto, alguns aplicativos dão suporte a atributos personalizados e o serviço de provisionamento do Azure AD pode ler e gravar em atributos personalizados. Para inserir suas definições no portal do Azure, marque a caixa de seleção **Mostrar opções avançadas** na parte inferior da tela **mapeamento de atributos** e selecione **Editar lista de atributos para** seu aplicativo.

Os aplicativos e sistemas que dão suporte à personalização da lista de atributos incluem:

- Salesforce
- ServiceNow
- Workday
- Azure Active Directory (há suporte para[atributos padrão do Azure AD API do Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity) e extensões de diretório personalizadas)
- Aplicativos que dão suporte a [SCIM 2,0](https://tools.ietf.org/html/rfc7643), em que os atributos definidos no [esquema principal](https://tools.ietf.org/html/rfc7643) precisam ser adicionados

> [!NOTE]
> A edição da lista de atributos com suporte é recomendada apenas para administradores que personalizaram o esquema de seus aplicativos e sistemas e têm conhecimento inicial de como seus atributos personalizados foram definidos. Isso às vezes requer familiaridade com as APIs e as ferramentas de desenvolvedor fornecidas por um aplicativo ou sistema.

Ao editar a lista de atributos com suporte, as seguintes propriedades são fornecidas:

- **Nome** – o nome do sistema do atributo, conforme definido no esquema do objeto de destino.
- **Tipo** – o tipo de dados que o atributo armazena, conforme definido no esquema do objeto de destino, que pode ser um dos seguintes tipos:
  - O atributo *Binary* contém dados binários.
  - O atributo *booliano* contém um valor verdadeiro ou falso.
  - *DateTime* -o atributo contém uma cadeia de caracteres de data.
  - *Integer* -o atributo contém um inteiro.
  - *Reference* -o atributo contém uma ID que faz referência a um valor armazenado em outra tabela no aplicativo de destino.
  - *String* -o atributo contém uma cadeia de texto.
- **Chave primária?** -Se o atributo é definido como um campo de chave primária no esquema do objeto de destino.
- **Necessário?** -Se o atributo deve ser preenchido no aplicativo ou sistema de destino.
- **Vários valores?** -Se o atributo dá suporte a vários valores.
- **Caso exato?** -Se os valores de atributos são avaliados de maneira diferenciada de maiúsculas e minúsculas.
- **Expressão de API** -não use, a menos que seja instruído a fazer isso pela documentação para um conector de provisionamento específico (como workday).
- **Atributo de objeto referenciado** -se for um atributo de tipo de referência, esse menu permitirá que você selecione a tabela e o atributo no aplicativo de destino que contém o valor associado ao atributo. Por exemplo, se você tiver um atributo chamado "Department" cujo valor armazenado faz referência a um objeto em uma tabela "departamentos" separada, selecione "Departments.Name". As tabelas de referência e os campos de ID primária com suporte para um determinado aplicativo são pré-configurados e, no momento, não podem ser editados usando o portal do Azure, mas podem ser editados usando o [API do Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-configure-with-custom-target-attributes).

#### <a name="provisioning-a-custom-extension-attribute-to-a-scim-compliant-application"></a>Provisionando um atributo de extensão personalizado para um aplicativo compatível com SCIM
A RFC SCIM define um esquema de usuário e grupo principal, permitindo também que as extensões do esquema atendam às necessidades do seu aplicativo. Para adicionar um atributo personalizado a um aplicativo SCIM:
   1. Entre no portal de [Azure Active Directory](https://aad.portal.azure.com), selecione **aplicativos empresariais**, selecione seu aplicativo e, em seguida, selecione **provisionamento**.
   2. Em **mapeamentos**, selecione o objeto (usuário ou grupo) para o qual você deseja adicionar um atributo personalizado.
   3. Na parte inferior da página, selecione **Mostrar opções avançadas**.
   4. Selecione **Editar lista de atributos para AppName**.
   5. Na parte inferior da lista de atributos, insira informações sobre o atributo personalizado nos campos fornecidos. Em seguida, selecione **Adicionar atributo**.

Para aplicativos SCIM, o nome do atributo deve seguir o padrão mostrado no exemplo abaixo. O "CustomExtensionName" e o "CustomAttribute" podem ser personalizados de acordo com os requisitos do seu aplicativo, por exemplo: urn: IETF: params: SCIM: schemas: Extension: 2.0: CustomExtensionName: CustomAttribute

Essas instruções só são aplicáveis a aplicativos habilitados para SCIM. Aplicativos como ServiceNow e Salesforce não são integrados ao Azure AD usando SCIM e, portanto, não exigem esse namespace específico ao adicionar um atributo personalizado.

Atributos personalizados não podem ser atributos de referência ou atributos de valores múltiplos. Os atributos de extensão de vários valores personalizados atualmente têm suporte apenas para aplicativos na galeria.  
 
**Exemplo de representação de um usuário com um atributo de extensão:**

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

- O mapeamento de um appRoleAssignment no Azure AD para uma função em seu aplicativo requer que você transforme o atributo usando uma [expressão](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data). O atributo appRoleAssignment **não deve ser mapeado diretamente** para um atributo role sem usar uma expressão para analisar os detalhes da função. 

- **SingleAppRoleAssignment** 
  - **Quando usar:** Use a expressão SingleAppRoleAssignment para provisionar uma única função para um usuário e para especificar a função primária. 
  - **Como configurar:** Use as etapas descritas acima para navegar até a página Mapeamentos de atributo e use a expressão SingleAppRoleAssignment para mapear para o atributo funções. Há três atributos de função a serem escolhidos: (funções [Primary EQ "true"]. display, Roles [Primary EQ "true]. Type e Roles [Primary EQ" true "]. Value). Você pode optar por incluir qualquer um dos atributos de função ou todos eles em seus mapeamentos. Se você quiser incluir mais de um, basta adicionar um novo mapeamento e incluí-lo como o atributo de destino.  
  
  ![Adicionar SingleAppRoleAssignment](./media/customize-application-attributes/edit-attribute-singleapproleassignment.png)
  - **Itens a serem considerados**
    - Certifique-se de que várias funções não sejam atribuídas a um usuário. Não podemos garantir qual função será provisionada.
    
  - **Exemplo de saída** 

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
  - **Quando usar:** Use a expressão AppRoleAssignmentsComplex para provisionar várias funções para um usuário. 
  - **Como configurar:** Edite a lista de atributos com suporte, conforme descrito acima, para incluir um novo atributo para as funções: 
  
    ![Adicionar funções](./media/customize-application-attributes/add-roles.png)<br>

    Em seguida, use a expressão AppRoleAssignmentsComplex para mapear para o atributo de função personalizada, conforme mostrado na imagem abaixo:

    ![Adicionar AppRoleAssignmentsComplex](./media/customize-application-attributes/edit-attribute-approleassignmentscomplex.png)<br>
  - **Itens a serem considerados**
    - Todas as funções serão provisionadas como primárias = falso.
    - A POSTAgem contém o tipo de função. A solicitação de PATCH não contém o tipo. Estamos trabalhando para enviar o tipo em solicitações POST e PATCH.
    
  - **Exemplo de saída** 
  
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

Se você precisar reiniciar e redefinir seus mapeamentos existentes de volta para seu estado padrão, poderá marcar a caixa de seleção **restaurar mapeamentos padrão** e salvar a configuração. Isso define todos os mapeamentos como se o aplicativo acabou de ser adicionado ao seu locatário do Azure AD por meio da Galeria de aplicativos.

A seleção dessa opção forçará efetivamente uma ressincronização de todos os usuários enquanto o serviço de provisionamento estiver em execução.

> [!IMPORTANT]
> É altamente recomendável que o **status de provisionamento** seja definido como **desativado** antes de invocar essa opção.

## <a name="what-you-should-know"></a>O que deve saber

- Microsoft Azure AD fornece uma implementação eficiente de um processo de sincronização. Em um ambiente inicializado, somente objetos que exigem atualizações são processados durante um ciclo de sincronização.
- A atualização de mapeamentos de atributo tem um impacto no desempenho de um ciclo de sincronização. Uma atualização para a configuração de mapeamento de atributo requer que todos os objetos gerenciados sejam reavaliados.
- Uma prática recomendada é manter o número de alterações consecutivas em seus mapeamentos de atributo no mínimo.
- Não há suporte para a adição de um atributo de foto a ser provisionado para um aplicativo hoje, pois não é possível especificar o formato para sincronizar a foto. Você pode solicitar o recurso no [User Voice](https://feedback.azure.com/forums/169401-azure-active-directory)
- O atributo IsSoftDeleted geralmente faz parte dos mapeamentos padrão para um aplicativo. IsSoftdeleted pode ser verdadeiro em um dos quatro cenários (o usuário está fora do escopo devido à falta de atribuição do aplicativo, o usuário está fora do escopo devido à não reunião de um filtro de escopo, o usuário foi excluído de maneira reversível no Azure AD ou a propriedade AccountEnabled está definida como false  no usuário). 
- O serviço de provisionamento do Azure AD não dá suporte ao provisionamento de valores nulos

## <a name="next-steps"></a>Passos seguintes

- [Automatizar o utilizador aprovisionamento/desaprovisionamento às aplicações SaaS](user-provisioning.md)
- [Gravando expressões para mapeamentos de atributos](functions-for-customizing-application-data.md)
- [Filtros de âmbito para o aprovisionamento do utilizador](define-conditional-rules-for-provisioning-user-accounts.md)
- [Utilizar o SCIM para ativar o aprovisionamento automático de utilizadores e grupos do Azure Active Directory a aplicações](use-scim-to-provision-users-and-groups.md)
- [Lista de tutoriais sobre como integrar aplicações SaaS](../saas-apps/tutorial-list.md)
