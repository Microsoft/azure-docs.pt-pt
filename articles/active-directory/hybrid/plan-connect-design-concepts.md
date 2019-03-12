---
title: 'Azure AD Connect: Conceitos de design | Documentos da Microsoft'
description: Este tópico fornece detalhes sobre determinadas áreas de design de implementação
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
ms.openlocfilehash: 311ba489073805fdb034b435ab9e5e1ddc2c4e3c
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57535047"
---
# <a name="azure-ad-connect-design-concepts"></a>Azure AD Connect: Conceitos de design
O objetivo deste documento é descrever áreas que devem ser consideradas por meio de durante o design de implementação do Azure AD Connect. Este documento é uma descrição profunda sobre determinadas áreas e esses conceitos uma breve descrição também outros documentos.

## <a name="sourceanchor"></a>sourceAnchor
O atributo sourceAnchor é definido como *um imutável durante o tempo de vida de um objeto de atributo*. Ele identifica exclusivamente um objeto como sendo o mesmo objeto no local e no Azure AD. O atributo é também designado **immutableId** e os dois nomes são utilizados intercambiáveis.

A palavra imutável, que é "não é possível alterar", é importante neste documento. Uma vez que o valor desse atributo não pode ser alterado depois foi definida, é importante escolher um design que suporta o seu cenário.

O atributo é utilizado para os seguintes cenários:

* Quando um novo servidor de motor de sincronização é criado ou reconstruído após um cenário de recuperação após desastre, este atributo liga objetos existentes no Azure AD com objetos no local.
* Se mover de uma identidade apenas na cloud para um modelo de identidade sincronizados, em seguida, esse atributo permite que objetos objetos existentes de "disco rígido correspondência" no Azure AD com objetos no local.
* Se usar a Federação, em seguida, esse atributo em conjunto com o **userPrincipalName** é utilizada na afirmação para identificar exclusivamente um utilizador.

Este tópico aborda apenas sourceAnchor que diz respeito aos utilizadores. As mesmas regras se aplicam a todos os tipos de objeto, mas é apenas para os utilizadores que esse problema geralmente é uma preocupação.

### <a name="selecting-a-good-sourceanchor-attribute"></a>Selecionar um atributo sourceAnchor boa
O valor do atributo tem de seguir as seguintes regras:

* Menos de 60 carateres de comprimento
  * Carateres que não estão sendo a-z, A-Z ou 0 a 9 são codificados e como de 3 carateres
* Contém um caráter especial: &#92; ! # $ % & * + / = ? ^ &#96; { } | ~ < > ( ) ' ; : , [ ] " \@ _
* Tem de ser globalmente exclusivo
* Tem de ser uma cadeia de caracteres, inteiros ou binário
* Não deve ser baseado no nome do utilizador, uma vez que estes podem alterar
* Não deve ser sensíveis a maiúsculas e evitar os valores que podem variar por caso
* Deve ser atribuído quando o objeto é criado

Se o sourceAnchor selecionado não é do tipo string, em seguida, Base64Encode de ligar-se de AD do Azure o valor do atributo para garantir que não existem carateres especiais são apresentados. Se utilizar outro servidor de federação que o ADFS, certifique-se de que o servidor de podem também Base64Encode que o atributo.

O atributo sourceAnchor diferencia maiúsculas de minúsculas. Um valor de "Diogoandrade" não é igual a "diogoandrade". Mas não deve ter dois objetos diferentes com apenas uma diferença no caso.

Se tiver uma única floresta no local, em seguida, o atributo deve utilizar seja **objectGUID**. Isso também é o atributo utilizado quando utiliza as definições rápidas no Azure AD Connect e também o atributo utilizado pelo DirSync.

Se tiver várias florestas e não mover utilizadores entre florestas e domínios, em seguida, **objectGUID** é um bom atributo para utilizar o mesmo nesse caso.

Se mover utilizadores entre florestas e domínios, em seguida, tem de encontrar um atributo que não é alterada ou pode ser movido com os utilizadores durante a mudança. Uma abordagem recomendada é introduzir um atributo sintético. Um atributo que poderá conter algo parecido com um GUID seria adequado. Durante a criação do objeto, um novo GUID é criado e carimbo de uma sessão pelo utilizador. Uma regra de sincronização personalizados pode ser criada no servidor de motor de sincronização para criar este valor com base na **objectGUID** e atualizar o atributo selecionado no ADDS. Quando move o objeto, lembre-se de que também copiar o conteúdo deste valor.

Outra solução é escolher um atributo existente, que sabe que não se altera. Atributos comumente usados incluem **employeeID**. Se considerar um atributo que contém letras, certifique-se de que não há que nenhuma oportunidade de se o caso (maiúsculas versus minúscula) pode ser alteradas para o valor desse atributo. Atributos ruins que não devem ser utilizados incluem esses atributos com o nome do utilizador. Num casamento ou divorce, o nome é esperado para alterar, que não é permitido para este atributo. Isso também é um motivo por que, como atributos **userPrincipalName**, **correio**, e **targetAddress** não são mesmo possíveis selecionar na instalação do Azure AD Connect Assistente. Esses atributos também contêm o "\@" caráter, o qual não é permitida no sourceAnchor.

### <a name="changing-the-sourceanchor-attribute"></a>Alterar o atributo sourceAnchor
Não é possível alterar o valor do atributo sourceAnchor depois do objeto foi criado no Azure AD e a identidade está sincronizada.

Por esse motivo, as seguintes restrições aplicam-se ao Azure AD Connect:

* O atributo sourceAnchor só pode ser definido durante a instalação inicial. Se executar novamente o Assistente de instalação, esta opção é só de leitura. Se precisar de alterar esta definição, tem de desinstalar e reinstalar.
* Se instalou outro servidor do Azure AD Connect, tem de selecionar o mesmo atributo sourceAnchor utilizado como anteriormente. Se anteriormente tiver estado a utilizar o DirSync e mover para o Azure AD Connect, em seguida, tem de utilizar **objectGUID** , pois esse é o atributo utilizado pelo DirSync.
* Se o valor de sourceAnchor é alterado após o objeto tenha sido exportado para o Azure AD, em seguida, o Azure AD Connect, que emite um erro de sincronização e não permite mais alterações em que o objeto antes do problema e o sourceAnchor é alterado em diretor da origem y.

## <a name="using-ms-ds-consistencyguid-as-sourceanchor"></a>Usando o ms-DS-ConsistencyGuid como sourceAnchor
Por predefinição, o Azure AD Connect (versão 1.1.486.0 e mais antigos) utiliza o objectGUID como o atributo sourceAnchor. ObjectGUID é gerado pelo sistema. Não é possível especificar seu valor durante a criação de locais objetos do AD. Conforme explicado na seção [sourceAnchor](#sourceanchor), existem cenários em que tem de especificar o valor de sourceAnchor. Se os cenários são aplicáveis para si, tem de utilizar um atributo de AD configurável (por exemplo, ms-DS-ConsistencyGuid) como o atributo sourceAnchor.

O Azure AD Connect (versão 1.1.524.0 e o depois) agora facilita o uso do ms-DS-ConsistencyGuid como sourceAnchor atributo. Quando utilizar esta funcionalidade, o Azure AD Connect configura automaticamente as regras de sincronização para:

1. Utilize o ms-DS-ConsistencyGuid como o atributo sourceAnchor para objetos de utilizador. ObjectGUID é utilizada para outros tipos de objeto.

2. Para qualquer dado locais AD utilizador cujo atributo ms-DS-ConsistencyGuid não estiver preenchidas, o Azure escritas AD Connect que seu valor objectGUID de volta para o atributo ms-DS-ConsistencyGuid no Active Directory no local do objeto. Depois do atributo ms-DS-ConsistencyGuid é preenchido, o Azure AD Connect exporta, em seguida, o objeto para o Azure AD.

>[!NOTE]
> Uma vez uma local do objeto do AD é importado para o Azure AD Connect (que é, importado para o espaço conector do AD e projetado no Metaverso), não é possível alterar o respetivo valor de sourceAnchor mais. Para especificar o valor de sourceAnchor para uma dada locais AD de objeto, configure seu atributo ms-DS-ConsistencyGuid antes de serem importado para o Azure AD Connect.

### <a name="permission-required"></a>Permissão necessária
Para esta funcionalidade funcione, a conta do AD DS utilizada para sincronizar com o Active Directory no local tem de possuir permissão de escrita para o atributo ms-DS-ConsistencyGuid no Active Directory no local.

### <a name="how-to-enable-the-consistencyguid-feature---new-installation"></a>Como ativar a funcionalidade de ConsistencyGuid - nova instalação
Pode habilitar o uso do ConsistencyGuid como sourceAnchor durante a instalação de novo. Esta secção abrange a instalação do Express e personalizados nos detalhes.

  >[!NOTE]
  > Apenas as versões mais recentes do Azure AD Connect (1.1.524.0 e o depois) suporta a utilização de ConsistencyGuid como sourceAnchor durante a instalação de novo.

### <a name="how-to-enable-the-consistencyguid-feature"></a>Como ativar a funcionalidade de ConsistencyGuid
Atualmente, a funcionalidade só pode ser ativada durante a nova instalação Azure AD Connect.

#### <a name="express-installation"></a>Instalação rápida
Ao instalar o Azure AD Connect com o modo do Express, o Assistente do Azure AD Connect determina automaticamente o atributo de AD mais adequado para utilizar como o atributo sourceAnchor usando a seguinte lógica:

* Em primeiro lugar, o Assistente do Azure AD Connect consulta o seu inquilino do Azure AD para recuperar o atributo de AD utilizado como o atributo sourceAnchor na instalação do Azure AD Connect anterior (se houver). Se esta informação estiver disponível, o Azure AD Connect utiliza o mesmo atributo de AD.

  >[!NOTE]
  > Apenas as versões mais recentes do Azure AD Connect (1.1.524.0 e o depois) informações de arquivo no seu inquilino do Azure AD sobre o atributo sourceAnchor usados durante a instalação. As versões mais antigas do Azure AD Connect não o fizer.

* Se a informações sobre o atributo sourceAnchor utilizado não estiver disponíveis, o assistente verifica o estado do atributo ms-DS-ConsistencyGuid no Active Directory no local. Se o atributo não está configurado em qualquer objeto no diretório, o assistente utiliza o ms-DS-ConsistencyGuid como o atributo sourceAnchor. Se o atributo é configurado num ou mais objetos no diretório, o assistente conclui o atributo está a ser utilizado por outros aplicativos e não é adequado, como o atributo sourceAnchor...

* Nesse caso, o assistente retrocede para utilizar objectGUID como o atributo sourceAnchor.

* Assim que o atributo sourceAnchor é decidido, o assistente armazena as informações no seu inquilino do Azure AD. As informações serão utilizadas pela instalação do futura do Azure AD Connect.

Uma vez concluída a instalação do Express, o assistente apresenta o atributo que foi escolhido como o atributo de âncora de origem.

![Assistente apresenta o atributo de AD escolhido para sourceAnchor](./media/plan-connect-design-concepts/consistencyGuid-01.png)

#### <a name="custom-installation"></a>Instalação personalizada
Ao instalar o Azure AD Connect com o modo personalizado, o Assistente do Azure AD Connect fornece duas opções quando configurar o atributo sourceAnchor:

![Instalação personalizada - sourceAnchor configuração](./media/plan-connect-design-concepts/consistencyGuid-02.png)

| Definição | Descrição |
| --- | --- |
| Permitir que seja o Azure a gerir a âncora de por mim | Selecione esta opção se pretender que o Azure AD escolha o atributo por si. Se selecionar esta opção, o Assistente do Azure AD Connect aplica o mesmo [lógica de seleção de atributo sourceAnchor usada durante a instalação do Express](#express-installation). Semelhante à instalação do Express, o assistente apresenta o atributo que foi escolhido como o atributo de âncora de origem após a conclusão da instalação personalizada. |
| Um atributo específico | Selecione esta opção se pretender especificar um atributo do AD existente como o atributo sourceAnchor. |

### <a name="how-to-enable-the-consistencyguid-feature---existing-deployment"></a>Como ativar a funcionalidade de ConsistencyGuid - implementação existente
Se tiver uma implementação existente do Azure AD Connect que está a utilizar o objectGUID como o atributo de âncora de origem, pode mudá-lo para utilizar ConsistencyGuid em substituição.

>[!NOTE]
> Apenas as versões mais recentes do Azure AD Connect (1.1.552.0 e o depois) suporta a mudança do ObjectGuid para ConsistencyGuid como o atributo de âncora de origem.

Para mudar de objectGUID ConsistencyGuid como o atributo de âncora de origem:

1. Iniciar o Assistente do Azure AD Connect e clique em **configurar** para ir para a tela de tarefas.

2. Selecione o **configurar âncora de origem** opção de tarefas e clique em **próxima**.

   ![Ativar ConsistencyGuid para implementação existente – passo 2](./media/plan-connect-design-concepts/consistencyguidexistingdeployment01.png)

3. Introduza as credenciais de administrador do Azure AD e clique em **seguinte**.

4. Assistente do Azure AD Connect analisa o estado do atributo ms-DS-ConsistencyGuid no Active Directory no local. Se o atributo não está configurado em qualquer objeto no diretório, o Azure AD Connect conclui que nenhuma outra aplicação está atualmente a utilizar o atributo e é segura para utilizá-lo como o atributo de âncora de origem. Clique em **seguinte** para continuar.

   ![Ativar ConsistencyGuid para implementação existente - passo 4](./media/plan-connect-design-concepts/consistencyguidexistingdeployment02.png)

5. Na **pronto para configurar** ecrã, clique em **configurar** para tornar a alteração na configuração.

   ![Ativar ConsistencyGuid para implementação existente – passo 5](./media/plan-connect-design-concepts/consistencyguidexistingdeployment03.png)

6. Uma vez concluída a configuração, o assistente indica o que MS-DS-ConsistencyGuid agora está a ser utilizado como o atributo de âncora de origem.

   ![Ativar ConsistencyGuid para implementação existente – passo 6](./media/plan-connect-design-concepts/consistencyguidexistingdeployment04.png)

Durante a análise (etapa 4), se o atributo é configurado num ou mais objetos no diretório, o assistente conclui o atributo está a ser utilizado por outra aplicação e devolve um erro, conforme ilustrado no diagrama abaixo. Este erro também pode ocorrer se anteriormente tiver ativado a funcionalidade de ConsistencyGuid no Azure AD Connect com seu principal servidor e está a tentar fazer o mesmo no seu servidor de preparação.

![Ativar ConsistencyGuid para implementação existente - erro](./media/plan-connect-design-concepts/consistencyguidexistingdeploymenterror.png)

 Se tiver a certeza de que o atributo não é usado por outros aplicativos existentes, pode suprimir o erro reiniciando o Assistente do Azure AD Connect com o **/SkipLdapSearch** comutador especificado. Para tal, execute o seguinte comando numa linha de comandos:

```
"c:\Program Files\Microsoft Azure Active Directory Connect\AzureADConnect.exe" /SkipLdapSearch
```

### <a name="impact-on-ad-fs-or-third-party-federation-configuration"></a>Impacto no AD FS ou de configuração de Federação de terceiros
Se estiver a utilizar o Azure AD Connect gerir a implementação no local do AD FS, o Azure AD Connect atualiza automaticamente as regras de afirmação a utilizar o mesmo atributo de AD como sourceAnchor. Isto garante que a afirmação de ImmutableID gerada pelo ADFS é consistente com os valores de sourceAnchor exportados para o Azure AD.

Se estiver a gerir o AD FS fora do Azure AD Connect ou estiver a utilizar servidores de Federação de terceiros para a autenticação, tem de atualizar manualmente as regras de afirmação da afirmação de ImmutableID ser consistente com os valores de sourceAnchor exportados para o Azure AD, conforme descrito em secção de artigos [as regras de afirmação de modificar o AD FS](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-federation-management#modclaims). O assistente retorna o seguinte aviso após a conclusão da instalação:

![Configuração de Federação de terceiros](./media/plan-connect-design-concepts/consistencyGuid-03.png)

### <a name="adding-new-directories-to-existing-deployment"></a>Adição de novos diretórios a implementação existente
Suponha que tiver implementado o Azure AD Connect com a funcionalidade de ConsistencyGuid ativada e, agora gostaria de adicionar outro diretório para a implementação. Quando tentar adicionar o diretório, o Assistente do Azure AD Connect verifica o estado do atributo ms-DS-ConsistencyGuid no diretório. Se o atributo é configurado num ou mais objetos no diretório, o assistente conclui o atributo está a ser utilizado por outros aplicativos e devolve um erro, conforme ilustrado no diagrama abaixo. Se tiver a certeza de que o atributo não é utilizado por aplicações existentes, pode suprimir o erro reiniciando o Assistente do Azure AD Connect com o **/SkipLdapSearch** comutador especificada conforme descrito acima, ou se precisar de entrar em contacto Suporte para obter mais informações.

![Adição de novos diretórios a implementação existente](./media/plan-connect-design-concepts/consistencyGuid-04.png)

## <a name="azure-ad-sign-in"></a>Início de sessão no Azure AD
Ao integrar o seu diretório no local com o Azure AD, é importante compreender como as definições de sincronização podem afetar o usuário de forma autentica. Azure AD utiliza userPrincipalName (UPN) para autenticar o utilizador. No entanto, ao sincronizar os seus utilizadores, tem de escolher o atributo a ser utilizado para o valor de userPrincipalName cuidadosamente.

### <a name="choosing-the-attribute-for-userprincipalname"></a>Escolha o atributo de userPrincipalName
Quando selecionar o atributo para fornecer deve certificar-se o valor do UPN para ser usado numa do Azure

* Deve ter o atributo valores estão em conformidade com a sintaxe UPN (RFC 822), que é o nome de utilizador do formato\@domínio
* O sufixo nos valores corresponde a um dos domínios personalizados verificados no Azure AD

As definições rápidas, a escolha assumida para o atributo é userPrincipalName. Se o atributo userPrincipalName não contém o valor que pretende que os utilizadores para iniciar sessão no Azure, em seguida, tem de escolher **instalação personalizada**.

### <a name="custom-domain-state-and-upn"></a>Estado do domínio personalizado e UPN
É importante certificar-se de que existe um domínio verificado para o sufixo UPN.

John é um utilizador em contoso.com. Pretende que o João para utilizar o john UPN no local\@contoso.com para iniciar sessão no Azure depois de sincronizados os usuários para seu contoso.onmicrosoft.com de diretório do Azure AD. Para tal, terá de adicionar e verificar o contoso.com como um domínio personalizado no Azure AD antes de começar a sincronizar os utilizadores. Se o sufixo UPN de John, por exemplo, contoso.com, não corresponde um domínio verificado no Azure AD, o Azure AD substitui o sufixo UPN por contoso.onmicrosoft.com.

### <a name="non-routable-on-premises-domains-and-upn-for-azure-ad"></a>Não encaminháveis internos domínios no local e o UPN para o Azure AD
Algumas organizações têm domínios não encaminháveis internos, como contoso. local, ou em domínios de etiqueta única simples, como contoso. Não é possível verificar um domínio não encaminháveis internos no Azure AD. O Azure AD Connect pode sincronizar a apenas um domínio verificado no Azure AD. Quando cria um diretório do Azure AD, ele cria um domínio encaminhável que se torna o domínio predefinido do Azure AD, por exemplo, contoso.onmicrosoft.com. Por conseguinte, se torna necessário verificar qualquer outro domínio encaminhável neste cenário, caso não deseja sincronizar com do domínio onmicrosoft.com predefinido.

Leia [adicionar o seu nome de domínio personalizado ao Azure Active Directory](../active-directory-domains-add-azure-portal.md) para obter mais informações sobre a adição e a verificação de domínios.

O Azure AD Connect Deteta se estiver a executar num ambiente de domínio não encaminháveis internos e seria adequadamente avisá-lo de aceder à frente com as definições rápidas. Se estiver a utilizar num domínio não encaminháveis internos, em seguida, é provável que o UPN, os utilizadores, tem também não encaminháveis internos sufixos. Por exemplo, se estiver a executar em contoso. local, o Azure AD Connect sugere a utilização de definições personalizadas, em vez de utilizar as definições rápidas. Utilizar as definições personalizadas, é capazes de especificar o atributo que deve ser utilizado como o UPN para iniciar sessão no Azure depois dos utilizadores são sincronizados com o Azure AD.

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
