---
title: 'Azure AD Connect: Conceitos de design | Microsoft Docs'
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
ms.openlocfilehash: baa03499cc11bda24ead986dd64621572484cbb1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89279657"
---
# <a name="azure-ad-connect-design-concepts"></a>Azure AD Connect: Conceitos de design
O objetivo deste documento é descrever áreas que devem ser pensadas durante o desenho de implementação do Azure AD Connect. Este documento é um profundo mergulho em certas áreas e estes conceitos são brevemente descritos em outros documentos também.

## <a name="sourceanchor"></a>fonteAnchor
O atributo fonteAnchor é definido como *um atributo imutável durante a vida útil de um objeto*. Identifica exclusivamente um objeto como sendo o mesmo objeto no local e em Azure AD. O atributo também é chamado **imutávelId** e os dois nomes são usados intercambiáveis.

A palavra imutável, que é "não pode ser alterada", é importante para este documento. Uma vez que o valor deste atributo não pode ser alterado depois de definido, é importante escolher um design que suporte o seu cenário.

O atributo é utilizado para os seguintes cenários:

* Quando um novo servidor de motor de sincronização é construído ou reconstruído após um cenário de recuperação de desastres, este atributo liga os objetos existentes em AZure AD com objetos no local.
* Se passar de uma identidade apenas em nuvem para um modelo de identidade sincronizado, então este atributo permite que os objetos "duros" os objetos existentes em AZure AD com objetos no local.
* Se utilizar a federação, então este atributo juntamente com o **userPrincipalName** é utilizado na reivindicação de identificar de forma única um utilizador.

Este tópico fala apenas da fonteAnchor, uma vez que se trata dos utilizadores. As mesmas regras aplicam-se a todos os tipos de objetos, mas é apenas para os utilizadores que este problema costuma ser uma preocupação.

### <a name="selecting-a-good-sourceanchor-attribute"></a>Selecionando um atributo De Boa FonteAnchor
O valor do atributo deve seguir as seguintes regras:

* Menos de 60 caracteres de comprimento
  * Os caracteres que não são a-z, A-Z ou 0-9 são codificados e contados como 3 caracteres
* Não contêm um carácter especial: &#92;! $ % & * + = = ? ^ &#96; { } | ~ < > . : , [ ] " \@ _
* Deve ser globalmente único
* Deve ser uma corda, inteiro, ou binário
* Não deve basear-se no nome do utilizador porque estes podem mudar
* Não deve ser sensível a casos e evitar valores que podem variar por caso
* Deve ser atribuído quando o objeto é criado

Se a fonte seleccionadaAnchor não for de tipo de cadeia, então Azure AD Connect Base64Encode o valor do atributo para garantir que não aparecem caracteres especiais. Se utilizar outro servidor da federação que não o ADFS, certifique-se de que o seu servidor também pode codificar o atributo Base64Encode.

O atributo OrigemAnchor é sensível a casos. Um valor de "JohnDoe" não é o mesmo que "johndoe". Mas não deve ter dois objetos diferentes com apenas uma diferença no caso.

Se tiver uma única floresta no local, então o atributo que deve utilizar é **objectGUID**. Este é também o atributo utilizado quando utiliza definições expressas no Azure AD Connect e também o atributo utilizado pela DirSync.

Se você tem várias florestas e não move os utilizadores entre florestas e domínios, então **objectGUID** é um bom atributo para usar mesmo neste caso.

Se deslocar os utilizadores entre florestas e domínios, então tem de encontrar um atributo que não mude ou possa ser movido com os utilizadores durante o movimento. Uma abordagem recomendada é introduzir um atributo sintético. Um atributo que poderia conter algo que se parecesse com um GUID seria adequado. Durante a criação de objetos, é criado e carimbado um novo GUID no utilizador. Uma regra de sincronização personalizada pode ser criada no servidor do motor de sincronização para criar este valor com base no **objectGUID** e atualizar o atributo selecionado em ADDS. Ao mover o objeto, certifique-se de copiar também o conteúdo deste valor.

Outra solução é escolher um atributo existente que sabe que não muda. Os atributos geralmente usados incluem **o employeeID.** Se considerar um atributo que contenha letras, certifique-se de que não há hipótese de o caso (maiúscula vs. minúscula) poder alterar para o valor do atributo. Os maus atributos que não devem ser utilizados incluem esses atributos com o nome do utilizador. Num casamento ou divórcio, espera-se que o nome mude, o que não é permitido para este atributo. Esta é também uma das razões pelas quais atributos como **userPrincipalName**, **mail** e **targetAddress** nem sequer são possíveis de selecionar no assistente de instalação Azure AD Connect. Esses atributos também contêm o " \@ " " personagem, que não é permitido na fonteAnchor.

### <a name="changing-the-sourceanchor-attribute"></a>Alterar o atributo SourceAnchor
O valor do atributo sourceAnchor não pode ser alterado após o objeto ter sido criado em Azure AD e a identidade ser sincronizada.

Por esta razão, aplicam-se as seguintes restrições ao Azure AD Connect:

* O atributo fonteAnchor só pode ser definido durante a instalação inicial. Se voltar a fazer a reexecução do assistente de instalação, esta opção é apenas de leitura. Se precisar de alterar esta definição, tem de desinstalar e reinstalar.
* Se instalar outro servidor Azure AD Connect, terá de selecionar o mesmo atributo De origemAnchor que anteriormente utilizado. Se já esteve a usar o DirSync e mudar-se para a Azure AD Connect, então deve utilizar **o objectGUID,** uma vez que é esse o atributo utilizado pela DirSync.
* Se o valor da fonteAnchor for alterado após o objeto ter sido exportado para Azure AD, então a sincronização Azure AD Connect lança um erro e não permite mais alterações nesse objeto antes de o problema ser corrigido e a fonteAnchor é alterada de volta no diretório de origem.

## <a name="using-ms-ds-consistencyguid-as-sourceanchor"></a>Usando ms-DS-ConsistênciaGuid como fonteAnchor
Por predefinição, o Azure AD Connect (versão 1.1.486.0 ou mais antigo) utiliza o objectGUID como o atributo SourceAnchor. ObjectGUID é gerado pelo sistema. Não é possível especificar o seu valor ao criar objetos AD no local. Como explicado na [secção fonteAnchor,](#sourceanchor)existem cenários em que é necessário especificar o valor da origemAnchor. Se os cenários forem aplicáveis a si, deve utilizar um atributo AD configurável (por exemplo, ms-DS-ConsistencyGuid) como o atributo SourceAnchor.

O Azure AD Connect (versão 1.1.524.0 e depois) facilita agora a utilização do ms-DS-ConsistênciaGuid como atributo sourceAnchor. Ao utilizar esta função, o Azure AD Connect configura automaticamente as regras de sincronização para:

1. Utilize o ms-DS-ConsistencyGuid como o atributo SourceAnchor para objetos do Utilizador. ObjectGUID é utilizado para outros tipos de objetos.

2. Para qualquer objeto de utilizador AD no local cujo atributo ms-DS-ConsistênciayGuid não é povoado, a Azure AD Connect escreve o seu valor objectGUID de volta ao atributo ms-DS-ConsistencyGuid no Ative Directory. Após o atributo ms-DS-ConsistencyGuid ser povoado, a Azure AD Connect exporta então o objeto para Azure AD.

>[!NOTE]
> Uma vez que um objeto AD no local é importado para Azure AD Connect (isto é, importado para o Espaço de Conector AD e projetado para o Metaverse), não pode mais alterar o seu valor de origemAnchor. Para especificar o valor de origemAnchor para um determinado objeto AD no local, configufique o seu atributo MS-DS-ConsistênciaYGuid antes de ser importado para Azure AD Connect.

### <a name="permission-required"></a>Permissão necessária
Para que esta funcionalidade funcione, a conta DS AD utilizada para sincronizar com o Ative Directory no local deve ser concedida permissão de escrita para o atributo MS-DS-ConsistencyGuid no Ative Directory no local.

### <a name="how-to-enable-the-consistencyguid-feature---new-installation"></a>Como ativar a funcionalidade ConsistencyGuid - Nova instalação
Pode ativar a utilização do ConsistencyGuid como fonteAnchor durante a nova instalação. Esta secção abrange a instalação express e personalizada em detalhes.

  >[!NOTE]
  > Apenas versões mais recentes do Azure AD Connect (1.1.524.0 e depois) suportam a utilização da ConsistencyGuid como fonteAnchor durante a nova instalação.

### <a name="how-to-enable-the-consistencyguid-feature"></a>Como ativar a funcionalidade ConsistencyGuid

#### <a name="express-installation"></a>Instalação Express
Ao instalar o Azure AD Connect com o modo Express, o assistente Azure AD Connect determina automaticamente o atributo AD mais adequado para usar como o atributo SourceAnchor utilizando a seguinte lógica:

* Em primeiro lugar, o assistente Azure AD Connect consulta o seu inquilino AZure AD para recuperar o atributo AD utilizado como o atributo SourceAnchor na instalação anterior do Azure AD Connect (se houver). Se esta informação estiver disponível, o Azure AD Connect utiliza o mesmo atributo AD.

  >[!NOTE]
  > Apenas versões mais recentes do Azure AD Connect (1.1.524.0 e depois) armazenam informações no seu inquilino AZURE AD sobre o atributo SourceAnchor utilizado durante a instalação. Versões mais antigas do Azure AD Connect não.

* Se não estiver disponível informações sobre o atributo SourceAnchor utilizado, o assistente verifica o estado do atributo MS-DS-ConsistencyGuid no seu Ative Directory no local. Se o atributo não estiver configurado em nenhum objeto do diretório, o assistente utiliza o ms-DS-ConsistencyGuid como o atributo SourceAnchor. Se o atributo estiver configurado num ou mais objetos no diretório, o assistente conclui que o atributo está a ser utilizado por outras aplicações e não é adequado como atributo sourceAnchor...

* Nesse caso, o assistente volta a utilizar o objectGUID como o atributo SourceAnchor.

* Uma vez decidido o atributo SourceAnchor, o assistente armazena a informação no seu inquilino AZure AD. A informação será utilizada através da futura instalação do Azure AD Connect.

Uma vez concluída a instalação do Expresso, o assistente informa-o qual o atributo foi escolhido como atributo Âncora de Origem.

![Assistente informa atributo AD escolhido para origemAnchor](./media/plan-connect-design-concepts/consistencyGuid-01.png)

#### <a name="custom-installation"></a>Instalação personalizada
Ao instalar o Azure AD Connect com o modo Personalizado, o assistente Azure AD Connect fornece duas opções ao configurar o atributo SourceAnchor:

![Instalação personalizada - configuração sourceAnchor](./media/plan-connect-design-concepts/consistencyGuid-02.png)

| Definições | Descrição |
| --- | --- |
| Permitir que seja o Azure a gerir a âncora de por mim | Selecione esta opção se pretender que o Azure AD escolha o atributo por si. Se selecionar esta opção, o assistente Azure AD Connect aplica a mesma [lógica de seleção de atributos de fonte utilizada durante a instalação express](#express-installation). Semelhante à instalação Express, o assistente informa-o qual atributo foi escolhido como o atributo Âncora de Origem após a conclusão da instalação Personalizada. |
| Um atributo específico | Selecione esta opção se pretender especificar um atributo do AD existente como o atributo sourceAnchor. |

### <a name="how-to-enable-the-consistencyguid-feature---existing-deployment"></a>Como ativar a funcionalidade ConsistencyGuid - Implementação existente
Se tiver uma implementação existente do Azure AD Connect que está a utilizar o objectGUID como atributo 'Âncora de Origem', pode alterá-lo para utilizar o ConsistencyGuid.

>[!NOTE]
> Apenas versões mais recentes do Azure AD Connect (1.1.552.0 e depois) suportam a troca de ObjectGuid para ConsistencyGuid como o atributo Source Anchor.

Para mudar de objectGUID para ConsistencyGuid como o atributo Âncora de Origem:

1. Inicie o assistente Azure AD Connect e clique em **Configurar** para ir ao ecrã 'Tarefas'.

2. Selecione a opção de tarefa **Configure Source Anchor** e clique em **Seguinte**.

   ![Ativar o YGuid para a implantação existente - passo 2](./media/plan-connect-design-concepts/consistencyguidexistingdeployment01.png)

3. Insira as suas credenciais de Administrador AD Azure e clique em **Seguinte**.

4. O assistente Azure AD Connect analisa o estado do atributo ms-DS-ConsistencyGuid no seu Ative Directory no local. Se o atributo não estiver configurado em nenhum objeto do diretório, o Azure AD Connect conclui que nenhuma outra aplicação está atualmente a utilizar o atributo e é segura para usá-lo como atributo Source Anchor. Clique em **Next** (Seguinte) para continuar.

   ![Ativar o YGuid para a implantação existente - passo 4](./media/plan-connect-design-concepts/consistencyguidexistingdeployment02.png)

5. No ecrã **Ready to Configure,** clique em **Configurar** para fazer a alteração da configuração.

   ![Ativar o YGuid para a implantação existente - passo 5](./media/plan-connect-design-concepts/consistencyguidexistingdeployment03.png)

6. Uma vez concluída a configuração, o assistente indica que o Ms-DS-ConsistencyGuid está agora a ser utilizado como atributo Âncora de Origem.

   ![Ativar o YGuid para a implantação existente - passo 6](./media/plan-connect-design-concepts/consistencyguidexistingdeployment04.png)

Durante a análise (passo 4), se o atributo estiver configurado num ou mais objetos no diretório, o assistente conclui que o atributo está a ser utilizado por outra aplicação e devolve um erro como ilustrado no diagrama abaixo. Este erro também pode ocorrer se tiver ativado previamente a função 'Tecnologia's Consistência no seu servidor principal Azure AD Connect e estiver a tentar fazer o mesmo no seu servidor de paragem.

![Ativar o YR para a implantação existente - erro](./media/plan-connect-design-concepts/consistencyguidexistingdeploymenterror.png)

 Se tiver a certeza de que o atributo não é utilizado por outras aplicações existentes, pode suprimir o erro reiniciando o assistente Azure AD Connect com o interruptor **/SkipLdapSearch** especificado. Para tal, executar o seguinte comando no comando imediatamente:

```
"c:\Program Files\Microsoft Azure Active Directory Connect\AzureADConnect.exe" /SkipLdapSearch
```

### <a name="impact-on-ad-fs-or-third-party-federation-configuration"></a>Impacto na configuração da FS AD ou da federação de terceiros
Se estiver a utilizar o Azure AD Connect para gerir a implementação de AD FS no local, o Azure AD Connect atualiza automaticamente as regras de reclamação para utilizar o mesmo atributo AD que fonteAnchor. Isto garante que a alegação ImutávelID gerada pela ADFS é consistente com os valores de origemAnchor exportados para Azure AD.

Se estiver a gerir o AD FS fora do Azure AD Connect ou estiver a utilizar servidores de federação de terceiros para autenticação, deve atualizar manualmente as regras de reclamação para imutávelID alegando ser consistente com os valores de origemAnchor exportados para Azure AD como descrito na secção artigo [Modificar as regras de reclamação da AD FS](./how-to-connect-fed-management.md#modclaims). O assistente retorna o seguinte aviso após a conclusão da instalação:

![Configuração da federação de terceiros](./media/plan-connect-design-concepts/consistencyGuid-03.png)

### <a name="adding-new-directories-to-existing-deployment"></a>Adicionar novos diretórios à implantação existente
Suponha que tenha implantado a Azure AD Connect com a funcionalidade ConsistencyGuid ativada, e agora gostaria de adicionar outro diretório à implementação. Quando tenta adicionar o diretório, o assistente Azure AD Connect verifica o estado do atributo ms-DS-ConsistencyGuid no diretório. Se o atributo estiver configurado num ou mais objetos no diretório, o assistente conclui que o atributo está a ser utilizado por outras aplicações e devolve um erro como ilustrado no diagrama abaixo. Se tiver a certeza de que o atributo não é utilizado pelas aplicações existentes, pode suprimir o erro reiniciando o assistente Azure AD Connect com o interruptor **/SkipLdapSearch** especificado como descrito acima ou precisa de contactar o Suporte para mais informações.

![Adicionar novos diretórios à implantação existente](./media/plan-connect-design-concepts/consistencyGuid-04.png)

## <a name="azure-ad-sign-in"></a>Azure AD sign-in
Ao integrar o seu diretório no local com a Azure AD, é importante entender como as definições de sincronização podem afetar a forma como o utilizador autentica. O Azure AD utiliza o userPrincipalName (UPN) para autenticar o utilizador. No entanto, ao sincronizar os seus utilizadores, tem de escolher cuidadosamente o atributo a utilizar para o valor do utilizadorPrincipalName.

### <a name="choosing-the-attribute-for-userprincipalname"></a>Escolher o atributo para userPrincipalName
Quando estiver a selecionar o atributo para fornecer o valor da UPN a ser usado em Azure deve-se assegurar

* Os valores de atributos estão em conformidade com a sintaxe UPN (RFC 822), ou seja, deve ser do domínio do nome de utilizador do formato \@
* O sufixo nos valores corresponde a um dos domínios personalizados verificados em Azure AD

Nas definições expressas, a escolha assumida para o atributo é o nome do utilizadorPrincipalName. Se o atributo UserPrincipalName não contiver o valor que pretende que os seus utilizadores inscrevam no Azure, então tem de escolher **a Instalação Personalizada**.

### <a name="custom-domain-state-and-upn"></a>Estado de domínio personalizado e UPN
É importante assegurar que existe um domínio verificado para o sufixo UPN.

John é um utilizador em contoso.com. Você quer que John use o upn no local john \@ contoso.com para iniciar seducação no Azure depois de ter sincronizado os utilizadores com o seu diretório AD AZure contoso.onmicrosoft.com. Para tal, é necessário adicionar e verificar contoso.com como um domínio personalizado em AZure AD antes de começar a sincronizar os utilizadores. Se o sufixo UPN de João, por exemplo contoso.com, não corresponder a um domínio verificado em Azure AD, então a Azure AD substitui o sufixo UPN por contoso.onmicrosoft.com.

### <a name="non-routable-on-premises-domains-and-upn-for-azure-ad"></a>Domínios não roteáveis nas instalações e UPN para Azure AD
Algumas organizações têm domínios não-encaminháveis, como contoso.local, ou domínios simples de rótulo único como contoso. Não é possível verificar um domínio não-encaminhável em Azure AD. O Azure AD Connect pode sincronizar apenas um domínio verificado em Azure AD. Quando cria um diretório AD Azure, cria um domínio de encaminhamento que se torna domínio predefinido para o seu AD Azure, por exemplo, contoso.onmicrosoft.com. Portanto, torna-se necessário verificar qualquer outro domínio de encaminhamento em tal cenário, caso não queira sincronizar com o domínio de onmicrosoft.com padrão.

Leia [Adicione o seu nome de domínio personalizado ao Azure Ative Directory](../fundamentals/add-custom-domain.md) para obter mais informações sobre a adição e verificação de domínios.

O Azure AD Connect deteta se estiver a funcionar num ambiente de domínio não roteado e avisa-o adequadamente de avançar com as definições expressas. Se estiver a operar num domínio não-encaminhável, é provável que a UPN, dos utilizadores, também tenha sufixos não-roteáveis. Por exemplo, se estiver a funcionar sob contoso.local, o Azure AD Connect sugere que utilize definições personalizadas em vez de utilizar definições expressas. Utilizando definições personalizadas, é possível especificar o atributo que deve ser usado como UPN para iniciar sação no Azure depois de os utilizadores serem sincronizados com a Azure AD.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).