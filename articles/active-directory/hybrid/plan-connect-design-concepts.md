---
title: 'Azure AD Connect: Conceitos de design | Microsoft Docs'
description: Este tópico detalha determinadas áreas de design de implementação
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 4114a6c0-f96a-493c-be74-1153666ce6c9
ms.service: active-directory
ms.custom: azure-ad-connect
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: Identity
ms.date: 08/10/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb41e14a7ecf41a2698a063c3067a98d8acf8f07
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70135742"
---
# <a name="azure-ad-connect-design-concepts"></a>Azure AD Connect: Conceitos de design
A finalidade deste documento é descrever as áreas que devem ser consideradas durante o design de implementação de Azure AD Connect. Este documento é um aprofundamento em determinadas áreas e esses conceitos também são descritos brevemente em outros documentos.

## <a name="sourceanchor"></a>sourceAnchor
O atributo sourceAnchor é definido como *um atributo imutável durante o tempo de vida de um objeto*. Ele identifica exclusivamente um objeto como sendo o mesmo objeto local e no Azure AD. O atributo também é chamado de imutávelid e os dois nomes são usados de forma intercambiável.

A palavra imutável, que é "não pode ser alterada", é importante para este documento. Como o valor deste atributo não pode ser alterado após ter sido definido, é importante escolher um design que dê suporte ao seu cenário.

O atributo é usado para os seguintes cenários:

* Quando um novo servidor do mecanismo de sincronização é criado ou recriado após um cenário de recuperação de desastre, esse atributo vincula objetos existentes no Azure AD a objetos locais.
* Se você mudar de uma identidade somente de nuvem para um modelo de identidade sincronizado, esse atributo permitirá que os objetos "correspondam" aos objetos existentes no Azure AD com objetos locais.
* Se você usar a Federação, esse atributo junto com o **userPrincipalName** será usado na declaração para identificar exclusivamente um usuário.

Este tópico fala apenas sobre sourceAnchor, já que ele se relaciona com os usuários. As mesmas regras se aplicam a todos os tipos de objeto, mas é apenas para os usuários em que esse problema geralmente é uma preocupação.

### <a name="selecting-a-good-sourceanchor-attribute"></a>Selecionando um bom atributo sourceAnchor
O valor do atributo deve seguir as seguintes regras:

* Menos de 60 caracteres de comprimento
  * Os caracteres que não são a-z, A-Z ou 0-9 são codificados e contados como 3 caracteres
* Não contém um caractere especial: &#92; ! # $ % & * + / = ? ^ &#96; { } | ~ < > () '; : , [ ] " \@ _
* Deve ser globalmente exclusivo
* Deve ser uma cadeia de caracteres, um inteiro ou um binário
* Não deve ser baseado no nome do usuário porque eles podem ser alterados
* Não deve diferenciar maiúsculas de minúsculas e evitar valores que podem variar de acordo com o caso
* Deve ser atribuído quando o objeto é criado

Se o sourceAnchor selecionado não for do tipo cadeia de caracteres, Azure AD Connect Base64Encode o valor do atributo para garantir que nenhum caractere especial apareça. Se você usar outro servidor de Federação do que o ADFS, verifique se o servidor também pode Base64Encode o atributo.

O atributo sourceAnchor diferencia maiúsculas de minúsculas. Um valor de "davibarros" não é o mesmo que "davibarros". Mas você não deve ter dois objetos diferentes com apenas uma diferença no caso.

Se você tiver uma única floresta local, o atributo que você deve usar será objectGUID. Esse também é o atributo usado quando você usa as configurações expressas em Azure AD Connect e também o atributo usado pelo DirSync.

Se você tiver várias florestas e não mover os usuários entre florestas e domínios, o **objectGUID** será um bom atributo a ser usado mesmo nesse caso.

Se você mover usuários entre florestas e domínios, deverá encontrar um atributo que não seja alterado ou que possa ser movido com os usuários durante a movimentação. Uma abordagem recomendada é introduzir um atributo sintético. Um atributo que pode conter algo parecido com um GUID seria adequado. Durante a criação do objeto, um novo GUID é criado e carimbado no usuário. Uma regra de sincronização personalizada pode ser criada no servidor do mecanismo de sincronização para criar esse valor com base no objectGUID e atualizar o atributo selecionado no Adds. Ao mover o objeto, certifique-se também de copiar o conteúdo desse valor.

Outra solução é escolher um atributo existente que você sabe que não é alterado. Os atributos comumente usados incluem **EmployeeID**. Se você considerar um atributo que contém letras, certifique-se de que não há nenhuma chance de que o caso (maiúsculas e minúsculas) possa ser alterado para o valor do atributo. Atributos inválidos que não devem ser usados incluem esses atributos com o nome do usuário. Em um casamento ou divórcio, o nome deve ser alterado, o que não é permitido para esse atributo. Isso também é um dos motivos pelos quais atributos como **userPrincipalName**, **mail**e **targetAddress** não são nem mesmo possível selecionar no assistente de instalação do Azure ad Connect. Esses atributos também contêm o caractere\@"", que não é permitido no sourceAnchor.

### <a name="changing-the-sourceanchor-attribute"></a>Alterando o atributo sourceAnchor
O valor do atributo sourceAnchor não pode ser alterado depois que o objeto tiver sido criado no Azure AD e a identidade for sincronizada.

Por esse motivo, as seguintes restrições se aplicam a Azure AD Connect:

* O atributo sourceAnchor só pode ser definido durante a instalação inicial. Se você executar novamente o assistente de instalação, essa opção será somente leitura. Se você precisar alterar essa configuração, deverá desinstalar e reinstalar o.
* Se você instalar outro servidor de Azure AD Connect, deverá selecionar o mesmo atributo sourceAnchor que o usado anteriormente. Se você já usa o DirSync e move para Azure AD Connect, você deve usar objectGUID , uma vez que é o atributo usado pelo DirSync.
* Se o valor de sourceAnchor for alterado depois que o objeto tiver sido exportado para o Azure AD, Azure AD Connect a sincronização lançar um erro e não permitirá nenhuma alteração no objeto antes que o problema seja corrigido e o sourceAnchor seja alterado de volta no diretor de origem Iar.

## <a name="using-ms-ds-consistencyguid-as-sourceanchor"></a>Usando MS-DS-ConsistencyGuid como sourceAnchor
Por padrão, Azure AD Connect (versão 1.1.486.0 e mais antiga) usa objectGUID como o atributo sourceAnchor. ObjectGUID é gerado pelo sistema. Você não pode especificar seu valor ao criar objetos do AD local. Conforme explicado na seção [sourceAnchor](#sourceanchor), há situações em que você precisa especificar o valor de sourceAnchor. Se os cenários forem aplicáveis a você, você deverá usar um atributo do AD configurável (por exemplo, ms-DS-ConsistencyGuid) como o atributo sourceAnchor.

O Azure AD Connect (versão 1.1.524.0 e posterior) agora facilita o uso de MS-DS-ConsistencyGuid como atributo sourceAnchor. Ao usar esse recurso, Azure AD Connect configura automaticamente as regras de sincronização para:

1. Use ms-DS-ConsistencyGuid como o atributo sourceAnchor para objetos de usuário. ObjectGUID é usado para outros tipos de objeto.

2. Para qualquer objeto de usuário do AD local determinado cujo atributo ms-DS-ConsistencyGuid não esteja populado, Azure AD Connect grava seu valor objectGUID de volta para o atributo ms-DS-ConsistencyGuid no local Active Directory. Depois que o atributo ms-DS-ConsistencyGuid é populado, Azure AD Connect exporta o objeto para o Azure AD.

>[!NOTE]
> Depois que um objeto do AD local é importado para Azure AD Connect (ou seja, importado para o espaço do conector do AD e projetado para o metaverso), você não pode mais alterar seu valor sourceAnchor. Para especificar o valor de sourceAnchor para um determinado objeto do AD local, configure seu atributo ms-DS-ConsistencyGuid antes que ele seja importado para o Azure AD Connect.

### <a name="permission-required"></a>Permissão necessária
Para que esse recurso funcione, a conta de AD DS usada para sincronizar com o Active Directory local deve receber permissão de gravação para o atributo ms-DS-ConsistencyGuid na Active Directory local.

### <a name="how-to-enable-the-consistencyguid-feature---new-installation"></a>Como habilitar o recurso ConsistencyGuid – nova instalação
Você pode habilitar o uso de ConsistencyGuid como sourceAnchor durante a nova instalação. Esta seção aborda a instalação expressa e personalizada em detalhes.

  >[!NOTE]
  > Somente as versões mais recentes do Azure AD Connect (1.1.524.0 e posterior) dão suporte ao uso de ConsistencyGuid como sourceAnchor durante a nova instalação.

### <a name="how-to-enable-the-consistencyguid-feature"></a>Como habilitar o recurso ConsistencyGuid

#### <a name="express-installation"></a>Instalação expressa
Ao instalar Azure AD Connect com o modo expresso, o assistente de Azure AD Connect determina automaticamente o atributo do AD mais apropriado a ser usado como o atributo sourceAnchor usando a seguinte lógica:

* Primeiro, o assistente de Azure AD Connect consulta seu locatário do Azure AD para recuperar o atributo do AD usado como o atributo sourceAnchor na instalação anterior do Azure AD Connect (se houver). Se essas informações estiverem disponíveis, Azure AD Connect usará o mesmo atributo do AD.

  >[!NOTE]
  > Somente as versões mais recentes do Azure AD Connect (1.1.524.0 e posterior) armazenam informações em seu locatário do Azure AD sobre o atributo sourceAnchor usado durante a instalação. As versões mais antigas do Azure AD Connect não.

* Se as informações sobre o atributo sourceAnchor usado não estiverem disponíveis, o assistente verificará o estado do atributo ms-DS-ConsistencyGuid em seu Active Directory local. Se o atributo não estiver configurado em nenhum objeto no diretório, o assistente usará o MS-DS-ConsistencyGuid como o atributo sourceAnchor. Se o atributo estiver configurado em um ou mais objetos no diretório, o assistente concluirá que o atributo está sendo usado por outros aplicativos e não será adequado como atributo sourceAnchor...

* Nesse caso, o assistente volta a usar objectGUID como o atributo sourceAnchor.

* Depois que o atributo sourceAnchor for decidido, o assistente armazenará as informações em seu locatário do Azure AD. As informações serão usadas pela instalação futura do Azure AD Connect.

Quando a instalação expressa for concluída, o assistente informará qual atributo foi selecionado como o atributo de âncora de origem.

![O Assistente informa o atributo do AD selecionado para sourceAnchor](./media/plan-connect-design-concepts/consistencyGuid-01.png)

#### <a name="custom-installation"></a>Instalação personalizada
Ao instalar Azure AD Connect com o modo personalizado, o assistente de Azure AD Connect fornece duas opções ao configurar o atributo sourceAnchor:

![Instalação personalizada-configuração do sourceAnchor](./media/plan-connect-design-concepts/consistencyGuid-02.png)

| Definição | Descrição |
| --- | --- |
| Permitir que seja o Azure a gerir a âncora de por mim | Selecione esta opção se pretender que o Azure AD escolha o atributo por si. Se você selecionar essa opção, Azure AD Connect assistente aplicará a mesma [lógica de seleção de atributo sourceAnchor usada durante a instalação expressa](#express-installation). Semelhante à instalação expressa, o Assistente informa qual atributo foi selecionado como o atributo de âncora de origem após a conclusão da instalação personalizada. |
| Um atributo específico | Selecione esta opção se pretender especificar um atributo do AD existente como o atributo sourceAnchor. |

### <a name="how-to-enable-the-consistencyguid-feature---existing-deployment"></a>Como habilitar o recurso ConsistencyGuid – implantação existente
Se você tiver uma implantação de Azure AD Connect existente que está usando objectGUID como o atributo de âncora de origem, você poderá alterá-la para usar ConsistencyGuid em vez disso.

>[!NOTE]
> Somente as versões mais recentes do Azure AD Connect (1.1.552.0 e posterior) dão suporte à alternância de objectGUID para ConsistencyGuid como o atributo de âncora de origem.

Para alternar de objectGUID para ConsistencyGuid como o atributo de âncora de origem:

1. Inicie o assistente de Azure AD Connect e clique em **Configurar** para ir para a tela tarefas.

2. Selecione a opção **Configurar tarefa âncora de origem** e clique em **Avançar**.

   ![Habilitar ConsistencyGuid para implantação existente – etapa 2](./media/plan-connect-design-concepts/consistencyguidexistingdeployment01.png)

3. Insira suas credenciais de administrador do Azure AD e clique em **Avançar**.

4. Azure AD Connect assistente analisa o estado do atributo ms-DS-ConsistencyGuid no seu Active Directory local. Se o atributo não estiver configurado em nenhum objeto no diretório, Azure AD Connect concluirá que nenhum outro aplicativo está usando o atributo no momento e é seguro usá-lo como o atributo de âncora de origem. Clique em **Seguinte** para continuar.

   ![Habilitar ConsistencyGuid para implantação existente – etapa 4](./media/plan-connect-design-concepts/consistencyguidexistingdeployment02.png)

5. Na tela **pronto para configurar** , clique em **Configurar** para fazer a alteração de configuração.

   ![Habilitar ConsistencyGuid para implantação existente – etapa 5](./media/plan-connect-design-concepts/consistencyguidexistingdeployment03.png)

6. Quando a configuração for concluída, o assistente indicará que o MS-DS-ConsistencyGuid está sendo usado agora como o atributo de âncora de origem.

   ![Habilitar ConsistencyGuid para implantação existente – etapa 6](./media/plan-connect-design-concepts/consistencyguidexistingdeployment04.png)

Durante a análise (etapa 4), se o atributo estiver configurado em um ou mais objetos no diretório, o assistente concluirá que o atributo está sendo usado por outro aplicativo e retornará um erro, conforme ilustrado no diagrama a seguir. Esse erro também pode ocorrer se você tiver habilitado o recurso ConsistencyGuid anteriormente no seu servidor de Azure AD Connect primário e estiver tentando fazer o mesmo no servidor de preparo.

![Habilitar ConsistencyGuid para implantação existente-erro](./media/plan-connect-design-concepts/consistencyguidexistingdeploymenterror.png)

 Se você tiver certeza de que o atributo não é usado por outros aplicativos existentes, poderá suprimir o erro reiniciando o assistente de Azure AD Connect com a opção **/SkipLdapSearch** especificada. Para fazer isso, execute o seguinte comando no prompt de comando:

```
"c:\Program Files\Microsoft Azure Active Directory Connect\AzureADConnect.exe" /SkipLdapSearch
```

### <a name="impact-on-ad-fs-or-third-party-federation-configuration"></a>Impacto no AD FS ou na configuração de Federação de terceiros
Se você estiver usando Azure AD Connect para gerenciar a implantação de AD FS local, o Azure AD Connect atualizará automaticamente as regras de declaração para usar o mesmo atributo do AD que sourceAnchor. Isso garante que a declaração imutávelid gerada pelo ADFS seja consistente com os valores de sourceAnchor exportados para o Azure AD.

Se você estiver gerenciando AD FS fora do Azure AD Connect ou estiver usando servidores de Federação de terceiros para autenticação, deverá atualizar manualmente as regras de declaração para que a declaração imutável seja consistente com os valores de sourceAnchor exportados para o Azure AD, conforme descrito em seção de artigo [modificar AD FS regras de declaração](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-federation-management#modclaims). O assistente retornará o seguinte aviso após a conclusão da instalação:

![Configuração de Federação de terceiros](./media/plan-connect-design-concepts/consistencyGuid-03.png)

### <a name="adding-new-directories-to-existing-deployment"></a>Adicionando novos diretórios à implantação existente
Suponha que você implantou Azure AD Connect com o recurso ConsistencyGuid habilitado e agora deseja adicionar outro diretório à implantação. Quando você tenta adicionar o diretório, Azure AD Connect assistente verifica o estado do atributo ms-DS-ConsistencyGuid no diretório. Se o atributo estiver configurado em um ou mais objetos no diretório, o assistente concluirá que o atributo está sendo usado por outros aplicativos e retornará um erro, conforme ilustrado no diagrama a seguir. Se você tiver certeza de que o atributo não é usado por aplicativos existentes, poderá suprimir o erro reiniciando o assistente de Azure AD Connect com a opção **/SkipLdapSearch** especificada conforme descrito acima ou precisará entrar em contato com o suporte para obter mais informações .

![Adicionando novos diretórios à implantação existente](./media/plan-connect-design-concepts/consistencyGuid-04.png)

## <a name="azure-ad-sign-in"></a>Entrada do Azure AD
Ao integrar seu diretório local ao Azure AD, é importante entender como as configurações de sincronização podem afetar a forma de autenticação do usuário. O AD do Azure usa userPrincipalName (UPN) para autenticar o usuário. No entanto, ao sincronizar os usuários, você deve escolher o atributo a ser usado para o valor de userPrincipalName com cuidado.

### <a name="choosing-the-attribute-for-userprincipalname"></a>Escolhendo o atributo para userPrincipalName
Quando você estiver selecionando o atributo para fornecer o valor do UPN a ser usado no Azure, deve garantir

* Os valores de atributo estão em conformidade com a sintaxe de UPN (RFC 822), que é o formato nome\@de usuário domínio
* O sufixo nos valores corresponde a um dos domínios personalizados verificados no Azure AD

Nas configurações expressas, a opção presumida para o atributo é userPrincipalName. Se o atributo userPrincipalName não contiver o valor que você deseja que os usuários entrem no Azure, você deverá escolher **instalação personalizada**.

### <a name="custom-domain-state-and-upn"></a>Estado de domínio personalizado e UPN
É importante garantir que haja um domínio verificado para o sufixo UPN.

João é um usuário do contoso.com. Você deseja que João use o UPN local John\@contoso.com para entrar no Azure depois de sincronizar os usuários com o diretório do AD do Azure contoso.onmicrosoft.com. Para fazer isso, você precisa adicionar e verificar contoso.com como um domínio personalizado no Azure AD antes de poder iniciar a sincronização dos usuários. Se o sufixo UPN de João, por exemplo contoso.com, não corresponder a um domínio verificado no Azure AD, o Azure AD substituirá o sufixo UPN por contoso.onmicrosoft.com.

### <a name="non-routable-on-premises-domains-and-upn-for-azure-ad"></a>Domínios locais não roteáveis e UPN para o Azure AD
Algumas organizações têm domínios não roteáveis, como contoso. local ou domínios de rótulo único simples, como a contoso. Não é possível verificar um domínio não roteável no Azure AD. Azure AD Connect pode sincronizar somente com um domínio verificado no Azure AD. Quando você cria um diretório do AD do Azure, ele cria um domínio roteável que se torna o domínio padrão para o Azure AD, por exemplo, contoso.onmicrosoft.com. Portanto, ele se torna necessário para verificar qualquer outro domínio roteável nesse cenário, caso você não queira sincronizar com o domínio padrão do onmicrosoft.com.

Leia [Adicionar seu nome de domínio personalizado para Azure Active Directory](../active-directory-domains-add-azure-portal.md) para obter mais informações sobre como adicionar e verificar domínios.

O Azure AD Connect detecta se você está executando em um ambiente de domínio não roteável e o avisaria adequadamente com as configurações expressas. Se você estiver operando em um domínio não roteável, é provável que o UPN, dos usuários, também tenha sufixos não roteáveis. Por exemplo, se você estiver executando em contoso. local, Azure AD Connect sugere que você use configurações personalizadas em vez de usar as configurações expressas. Usando configurações personalizadas, você pode especificar o atributo que deve ser usado como UPN para entrar no Azure depois que os usuários são sincronizados com o Azure AD.

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
