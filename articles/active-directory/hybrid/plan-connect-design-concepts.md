---
title: 'Azure AD Connect: Conceitos de design / Microsoft Docs'
description: Este tópico detalha certas áreas de design de implementação
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79253887"
---
# <a name="azure-ad-connect-design-concepts"></a>Azure AD Connect: Conceitos de design
O objetivo deste documento é descrever áreas que devem ser pensadas durante o desenho de implementação do Azure AD Connect. Este documento é um profundo mergulho em determinadas áreas e estes conceitos são brevemente descritos em outros documentos também.

## <a name="sourceanchor"></a>fonteÂncora
O atributo sourceAnchor é definido como *um atributo imutável durante a vida de um objeto*. Identifica um objeto como sendo o mesmo objeto no local e em Azure D.D. O atributo também é chamado **imutável Id** e os dois nomes são usados permutáveis.

A palavra imutável, que é "não pode ser alterada", é importante para este documento. Uma vez que o valor deste atributo não pode ser alterado depois de definido, é importante escolher um design que suporte o seu cenário.

O atributo é utilizado para os seguintes cenários:

* Quando um novo servidor de motor sincronizado é construído, ou reconstruído após um cenário de recuperação de desastres, este atributo liga objetos existentes em Azure AD com objetos no local.
* Se passar de uma identidade apenas em nuvem para um modelo de identidade sincronizado, então este atributo permite que objetos "correspondam duramente" objetos existentes em Azure AD com objetos no local.
* Se utilizar a federação, este atributo juntamente com o **utilizador PrincipalName** é utilizado na alegação para identificar exclusivamente um utilizador.

Este tópico só fala sobre a fonteAnchor no que diz respeito aos utilizadores. As mesmas regras aplicam-se a todos os tipos de objetos, mas é apenas para os utilizadores que este problema normalmente é uma preocupação.

### <a name="selecting-a-good-sourceanchor-attribute"></a>Selecionando um atributo de boa fonteAnchor
O valor do atributo deve seguir as seguintes regras:

* Menos de 60 caracteres de comprimento
  * Os caracteres que não são a-z, A-Z ou 0-9 são codificados e contados como 3 caracteres
* Não conter um &#92; personagem especial: ! # $ % & * + / = ? ^ &#96; { } | ~ & lt; > ( ) ' : , [] \@ _
* Deve ser globalmente único
* Deve ser uma corda, inteiro, ou binário
* Não deve basear-se no nome do utilizador porque estes podem mudar
* Não deve ser sensível a casos e evitar valores que podem variar por caso
* Deve ser atribuído quando o objeto é criado

Se a fonte seleccionadaAnchor não for de cadeia de tipo, então azure AD Connect Base64Encode o valor do atributo para garantir que não aparecem caracteres especiais. Se utilizar outro servidor da federação que não o ADFS, certifique-se de que o seu servidor também pode codificar o atributo Base64En.

O atributo sourceAnchor é sensível a casos. Um valor de "JohnDoe" não é o mesmo que "johndoe". Mas não deve ter dois objetos diferentes com apenas uma diferença no caso.

Se tiver uma única floresta no local, então o atributo que deve usar é **objectGUID**. Este é também o atributo usado quando utiliza definições expressas no Azure AD Connect e também o atributo utilizado pelo DirSync.

Se tiver várias florestas e não mover utilizadores entre florestas e domínios, então o **objectGUID** é um bom atributo a usar mesmo neste caso.

Se deslocar os utilizadores entre florestas e domínios, então deve encontrar um atributo que não mude ou possa ser movido com os utilizadores durante a mudança. Uma abordagem recomendada é introduzir um atributo sintético. Um atributo que pudesse conter algo que se parecesse com um GUID seria adequado. Durante a criação de objetos, um novo GUID é criado e estampado no utilizador. Uma regra de sincronização personalizada pode ser criada no servidor do motor sincronizado para criar este valor com base no **objectGUID** e atualizar o atributo selecionado em ADDS. Quando mover o objeto, certifique-se de copiar também o conteúdo deste valor.

Outra solução é escolher um atributo existente que sabe que não muda. Os atributos comumente usados incluem **o id do empregado**. Se considerar um atributo que contenha letras, certifique-se de que não há hipótese de o caso (maiúsculo vs. minúscula) poder alterar para o valor do atributo. Os atributos maus que não devem ser utilizados incluem os atributos com o nome do utilizador. Num casamento ou divórcio, espera-se que o nome mude, o que não é permitido para este atributo. Esta é também uma das razões pelas quais atributos como **userPrincipalName**, **mail**, e **targetAddress** nem sequer são possíveis de selecionar no assistente de instalação Azure AD Connect. Esses atributos também contêm o caráter "\@", que não é permitido na fonteAnchor.

### <a name="changing-the-sourceanchor-attribute"></a>Alteração do atributo sourceAnchor
O valor do atributo sourceAnchor não pode ser alterado após a criação do objeto em Azure AD e a identidade é sincronizada.

Por esta razão, aplicam-se as seguintes restrições ao Azure AD Connect:

* O atributo sourceAnchor só pode ser definido durante a instalação inicial. Se reproduzir o assistente de instalação, esta opção é apenas para leitura. Se precisar de alterar esta definição, terá de desinstalar e reinstalar.
* Se instalar outro servidor Azure AD Connect, então deve selecionar o mesmo atributo sourceAnchor que o anteriormente utilizado. Se já esteve a utilizar o DirSync e mudar-se para o Azure AD Connect, então deve utilizar **o objectGUID,** uma vez que é esse o atributo utilizado pelo DirSync.
* Se o valor para a fonteAnchor for alterado após o objeto ter sido exportado para AD Azure, então a sincronização Azure AD Connect lança um erro e não permite mais alterações sobre esse objeto antes de o problema ter sido corrigido e a fonteAnchor é alterada novamente no diretor de origem Y.

## <a name="using-ms-ds-consistencyguid-as-sourceanchor"></a>Usando ms-DS-ConsistênciaGuid como fonteAnchor
Por predefinição, o Azure AD Connect (versão 1.1.486.0 e mais antigo) utiliza o objectGUID como atributo sourceAnchor. O ObjectGUID é gerado pelo sistema. Não é possível especificar o seu valor ao criar objetos AD no local. Como explicado na fonte da [secçãoAnchor,](#sourceanchor)existem cenários em que é necessário especificar o valor sourceAnchor. Se os cenários forem aplicáveis a si, deve utilizar um atributo ad configurável (por exemplo, ms-DS-Consistência) como atributo sourceAnchor.

O Azure AD Connect (versão 1.1.524.0 e depois) facilita agora a utilização de ms-DS-Consistência Como atributo sourceAnchor. Ao utilizar esta funcionalidade, o Azure AD Connect configura automaticamente as regras de sincronização para:

1. Utilize ms-DS-Consistência-Guid como o atributo sourceAnchor para objetos de utilizador. O ObjectGUID é utilizado para outros tipos de objetos.

2. Para qualquer objeto ad-utilizador no local cujo atributo Ms-DS-Consistência-Consistência Não é povoado, o Azure AD Connect escreve o seu valor objetivo de volta ao atributo ms-DS-Consistência-ConsistênciaGuid no Diretório Ativo no local. Após o atributo ms-DS-Consistência-Guia ser povoado, o Azure AD Connect exporta o objeto para AD Azure.

>[!NOTE]
> Uma vez que um objeto AD no local é importado para Azure AD Connect (isto é, importado para o Espaço de Conector AD e projetado para o Metaverse), não pode mais alterar o seu valor de origemAnchor. Para especificar o valor sourceAnchor para um determinado objeto AD no local, configure o seu atributo ms-DS-Consistência-Consistência Antes de ser importado para Azure AD Connect.

### <a name="permission-required"></a>Permissão necessária
Para que esta funcionalidade funcione, a conta AD DS utilizada para sincronizar com o Diretório Ativo no local deve ser concedida permissão de escrita para o atributo ms-DS-Consistência-Guid no diretório ativo no local.

### <a name="how-to-enable-the-consistencyguid-feature---new-installation"></a>Como ativar a funcionalidade ConsistênciaGuid - Nova instalação
Pode ativar a utilização do ConsistênciaGuid como fonteAnchor durante uma nova instalação. Esta secção cobre tanto a instalação Express como a Custom em detalhes.

  >[!NOTE]
  > Apenas as versões mais recentes do Azure AD Connect (1.1.524.0 e depois) suportam a utilização do ConsistênciaGuid como fonteAnchor durante a nova instalação.

### <a name="how-to-enable-the-consistencyguid-feature"></a>Como ativar a funcionalidade ConsistênciaGuid

#### <a name="express-installation"></a>Instalação Rápida
Ao instalar o Azure AD Connect com o modo Express, o assistente Azure AD Connect determina automaticamente o atributo AD mais adequado a utilizar como atributo sourceAnchor utilizando a seguinte lógica:

* Em primeiro lugar, o assistente Azure AD Connect consulta o seu inquilino Azure AD para recuperar o atributo AD usado como atributo sourceAnchor na instalação anterior do Azure AD Connect (se houver). Se esta informação estiver disponível, o Azure AD Connect utiliza o mesmo atributo AD.

  >[!NOTE]
  > Apenas versões mais recentes do Azure AD Connect (1.1.524.0 e depois) armazenam informações no seu inquilino Azure AD sobre o atributo sourceAnchor utilizado durante a instalação. Versões mais antigas do Azure AD Connect não.

* Se não houver informação sobre o atributo sourceAnchor utilizado, o assistente verifica o estado do atributo ms-DS-Consistência-Guia no seu Diretório Ativo no local. Se o atributo não estiver configurado em nenhum objeto no diretório, o assistente utiliza o ms-DS-ConsistênciaComo atributo sourceAnchor. Se o atributo estiver configurado em um ou mais objetos no diretório, o assistente conclui que o atributo está a ser utilizado por outras aplicações e não é adequado como atributo sourceAnchor...

* Nesse caso, o assistente volta a usar objectGUID como atributo sourceAnchor.

* Uma vez decidido o atributo sourceAnchor, o assistente armazena a informação no seu inquilino Azure AD. A informação será utilizada por futura instalação do Azure AD Connect.

Uma vez concluída a instalação Express, o assistente informa-o de que atributo foi escolhido como atributo Source Anchor.

![Assistente informa atributo ad escolhido para fonteAnchor](./media/plan-connect-design-concepts/consistencyGuid-01.png)

#### <a name="custom-installation"></a>Instalação personalizada
Ao instalar o Azure AD Connect com o modo Personalizado, o assistente Azure AD Connect fornece duas opções ao configurar o atributo sourceAnchor:

![Instalação personalizada - configuração sourceAnchor](./media/plan-connect-design-concepts/consistencyGuid-02.png)

| Definição | Descrição |
| --- | --- |
| Permitir que seja o Azure a gerir a âncora de por mim | Selecione esta opção se pretender que o Azure AD escolha o atributo por si. Se selecionar esta opção, o assistente Azure AD Connect aplica a mesma lógica de seleção de [atributos SourceAnchor utilizada durante](#express-installation)a instalação do Expresso . Semelhante à instalação Express, o assistente informa-o de que o atributo foi escolhido como atributo Source Anchor após a instalação personalizada estar concluída. |
| Um atributo específico | Selecione esta opção se pretender especificar um atributo do AD existente como o atributo sourceAnchor. |

### <a name="how-to-enable-the-consistencyguid-feature---existing-deployment"></a>Como ativar a funcionalidade ConsistênciaGuid - Implementação existente
Se tiver uma implementação de Ligação Azure AD connect existente que esteja a usar o objectGUID como atributo Source Anchor, pode mudá-la para utilizar o ConsistênciaGuid.

>[!NOTE]
> Apenas versões mais recentes do Azure AD Connect (1.1.552.0 e depois) suportam a mudança de ObjectGuid para Consistência Como atributo de Âncora Fonte.

Para mudar de objectGUID para ConsistênciaGuid como atributo de Âncora Fonte:

1. Inicie o assistente Azure AD Connect e clique em **Configurar** para ir ao ecrã 'Tarefas'.

2. Selecione a opção de tarefa de **âncora de origem configurar** e clique **em Next**.

   ![Ativar o ConsistênciaGuid para a implementação existente - passo 2](./media/plan-connect-design-concepts/consistencyguidexistingdeployment01.png)

3. Introduza as credenciais do Administrador AD Azure e clique em **Next**.

4. O assistente Azure AD Connect analisa o estado do atributo ms-DS-Consistência-ConsistênciaNo seu Diretório Ativo no local. Se o atributo não estiver configurado em nenhum objeto no diretório, o Azure AD Connect conclui que nenhuma outra aplicação está atualmente a usar o atributo e é segura para usá-lo como atributo Source Anchor. Clique em **Seguinte** para continuar.

   ![Ativar o ConsistênciaGuid para a implementação existente - passo 4](./media/plan-connect-design-concepts/consistencyguidexistingdeployment02.png)

5. No ecrã **Ready to Configure,** clique em **Configurar** para alterar a configuração.

   ![Ativar o ConsistênciaGuid para a implementação existente - passo 5](./media/plan-connect-design-concepts/consistencyguidexistingdeployment03.png)

6. Uma vez concluída a configuração, o assistente indica que ms-DS-Consistência Está agora a ser usado como atributo de Âncora Fonte.

   ![Ativar o ConsistênciaGuid para a implementação existente - passo 6](./media/plan-connect-design-concepts/consistencyguidexistingdeployment04.png)

Durante a análise (passo 4), se o atributo estiver configurado num ou mais objetos no diretório, o assistente conclui que o atributo está a ser utilizado por outra aplicação e devolve um erro ilustrado no diagrama abaixo. Este erro também pode ocorrer se tiver ativado previamente a funcionalidade ConsistênciaGuid no seu servidor Principal Azure AD Connect e estiver a tentar fazer o mesmo no seu servidor de encenação.

![Ativar o ConsistênciaGuid para a implementação existente - erro](./media/plan-connect-design-concepts/consistencyguidexistingdeploymenterror.png)

 Se tiver a certeza de que o atributo não é utilizado por outras aplicações existentes, pode suprimir o erro reiniciando o assistente Azure AD Connect com o interruptor **/SkipLdapSearch** especificado. Para isso, execute o seguinte comando em pedido de comando:

```
"c:\Program Files\Microsoft Azure Active Directory Connect\AzureADConnect.exe" /SkipLdapSearch
```

### <a name="impact-on-ad-fs-or-third-party-federation-configuration"></a>Impacto na configuração da AD FS ou da federação de terceiros
Se estiver a utilizar o Azure AD Connect para gerir a implementação do AD FS no local, o Azure AD Connect atualiza automaticamente as regras de reclamação para utilizar o mesmo atributo AD como fonteAnchor. Isto garante que a alegação Imutável de ID gerada pela ADFS é consistente com os valores sourceAnchor exportados para a AD Azure.

Se estiver a gerir a AD FS fora do Azure AD Connect ou estiver a utilizar servidores da federação de terceiros para autenticação, tem de atualizar manualmente as regras de reclamação para a alegação imutável de ID de origem consistente com os valores de origemAnchor exportados para AD Azure, conforme descrito na secção de artigos Modificar as regras de [reivindicação da AD FS](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-federation-management#modclaims). O assistente devolve o seguinte aviso após a instalação terminar:

![Configuração da federação de terceiros](./media/plan-connect-design-concepts/consistencyGuid-03.png)

### <a name="adding-new-directories-to-existing-deployment"></a>Adicionar novos diretórios à implantação existente
Suponha que implementou o Azure AD Connect com a funcionalidade ConsistênciaGuid ativada, e agora gostaria de adicionar outro diretório à implementação. Quando tenta adicionar o diretório, o assistente Azure AD Connect verifica o estado do atributo ms-DS-Consistência-Consistência No diretório. Se o atributo estiver configurado num ou mais objetos no diretório, o assistente conclui que o atributo está a ser utilizado por outras aplicações e devolve um erro ilustrado no diagrama abaixo. Se tiver a certeza de que o atributo não é utilizado pelas aplicações existentes, pode suprimir o erro reiniciando o assistente Azure AD Connect com o interruptor **/SkipLdapSearch** especificado como descrito acima ou precisa de contactar o Suporte para obter mais informações.

![Adicionar novos diretórios à implantação existente](./media/plan-connect-design-concepts/consistencyGuid-04.png)

## <a name="azure-ad-sign-in"></a>Entrada de entrada da AD Azure
Ao integrar o seu diretório no local com o Azure AD, é importante perceber como as definições de sincronização podem afetar a forma como o utilizador autentica. A Azure AD utiliza o userPrincipalName (UPN) para autenticar o utilizador. No entanto, ao sincronizar os seus utilizadores, deve escolher cuidadosamente o atributo a utilizar para o valor do utilizadorPrincipalName.

### <a name="choosing-the-attribute-for-userprincipalname"></a>Escolher o atributo para userPrincipalName
Quando estiver a selecionar o atributo para fornecer o valor da UPN a utilizar em Azure deve-se assegurar

* Os valores do atributo estão em conformidade com a sintaxe UPN (RFC 822), ou seja, deve ser do nome de utilizador do formato\@domínio
* O sufixo nos valores corresponde a um dos domínios personalizados verificados em Azure AD

Nas definições expressas, a escolha assumida para o atributo é o userPrincipalName. Se o atributo do userPrincipalName não contiver o valor que pretende que os seus utilizadores inscretem no Azure, então deve escolher a **Instalação Personalizada**.

### <a name="custom-domain-state-and-upn"></a>Estado de domínio personalizado e UPN
É importante garantir que existe um domínio verificado para o sufixo UPN.

John é um utilizador em contoso.com. Quer que o John utilize o upn\@contoso.com para iniciar sessão no Azure depois de ter sincronizado os utilizadores com o seu diretório Azure AD contoso.onmicrosoft.com. Para tal, tem de adicionar e verificar contoso.com como domínio personalizado no Azure AD antes de poder começar a sincronizar os utilizadores. Se o sufixo UPN de João, por exemplo contoso.com, não corresponder a um domínio verificado em Azure AD, então a Azure AD substitui o sufixo UPN por contoso.onmicrosoft.com.

### <a name="non-routable-on-premises-domains-and-upn-for-azure-ad"></a>Domínios no local não repreensíveis e UPN para AD Azure
Algumas organizações têm domínios não-repreensíveis, como contoso.local, ou domínios simples de rótulo único como contoso. Não é possível verificar um domínio não-repreensível em Azure AD. O Azure AD Connect só pode sincronizar um domínio verificado em Azure AD. Quando cria um diretório Azure AD, cria um domínio resaídaível que se torna domínio padrão para o seu Anúncio Azure, por exemplo, contoso.onmicrosoft.com. Portanto, torna-se necessário verificar qualquer outro domínio repreensível em tal cenário, caso não queira sincronizar com o domínio padrão onmicrosoft.com.

Leia Adicione o seu nome de [domínio personalizado ao Azure Ative Directory](../active-directory-domains-add-azure-portal.md) para obter mais informações sobre a adição e verificação de domínios.

O Azure AD Connect deteta se estiver a funcionar num ambiente de domínio não repreensível e o advertiria adequadamente para avançar com configurações expressas. Se estiver a operar num domínio não repreensível, então é provável que a UPN, dos utilizadores, também tenha sufixos não repreensíveis. Por exemplo, se estiver a correr sob contoso.local, o Azure AD Connect sugere que utilize configurações personalizadas em vez de utilizar configurações expressas. Utilizando configurações personalizadas, é possível especificar o atributo que deve ser usado como UPN para iniciar sessão no Azure depois de os utilizadores estarem sincronizados com a AD Azure.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
