---
title: Regras para a adesão de grupos de povoados dinamicamente - Azure AD [ AD ] Microsoft Docs
description: Como criar regras de adesão para povoar automaticamente grupos, e uma referência de regras.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/29/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a399ee43ef0ce97274f060b7a5b7df46fb523605
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82582897"
---
# <a name="dynamic-membership-rules-for-groups-in-azure-active-directory"></a>Regras dinâmicas de adesão para grupos no Diretório Ativo do Azure

No Azure Ative Directory (Azure AD), pode criar regras complexas baseadas em atributos para permitir adesões dinâmicas para grupos. A adesão ao grupo dinâmico reduz a sobrecarga administrativa de adicionar e remover utilizadores. Este artigo detalha as propriedades e a sintaxe para criar regras dinâmicas de adesão para utilizadores ou dispositivos. Pode configurar uma regra de filiação dinâmica em grupos de segurança ou grupos do Office 365.

Quando qualquer atributo de um utilizador ou dispositivo muda, o sistema avalia todas as regras dinâmicas do grupo num diretório para ver se a alteração iria desencadear qualquer atributo que adiciona ou remove. Se um utilizador ou dispositivo satisfaz em um grupo uma regra, eles são adicionados como membro desse grupo. Se já não cumprirem a regra, são removidos. Não é possível adicionar ou remover manualmente um membro de um grupo dinâmico.

- Pode criar um grupo dinâmico para dispositivos ou para utilizadores, mas não pode criar uma regra que contenha utilizadores e dispositivos.
- Não é possível criar um grupo de dispositivos com base nos atributos dos proprietários do dispositivo. As regras de adesão ao dispositivo só podem fazer referência aos atributos do dispositivo.

> [!NOTE]
> Esta funcionalidade requer uma licença Azure AD Premium P1 para cada utilizador único que seja membro de um ou mais grupos dinâmicos. Não é necessário atribuir licenças aos utilizadores para que sejam membros de grupos dinâmicos, mas deve ter o número mínimo de licenças na organização Azure AD para cobrir todos esses utilizadores. Por exemplo, se tivesse um total de 1.000 utilizadores únicos em todos os grupos dinâmicos da sua organização, precisaria de pelo menos 1.000 licenças para o Azure AD Premium P1 para satisfazer o requisito da licença.
> Não é necessária nenhuma licença para dispositivos que sejam membros de um grupo de dispositivos dinâmicos.

## <a name="rule-builder-in-the-azure-portal"></a>Construtor de regras no portal Azure

A Azure AD fornece um construtor de regras para criar e atualizar as suas regras importantes mais rapidamente. O construtor de regras apoia a construção até cinco expressões. O construtor de regras torna mais fácil formar uma regra com algumas expressões simples, no entanto, não pode ser usado para reproduzir todas as regras. Se o construtor de regras não apoiar a regra que pretende criar, pode utilizar a caixa de texto.

Aqui estão alguns exemplos de regras avançadas ou sintaxe para as quais recomendamos que construa usando a caixa de texto:

- Governar com mais de cinco expressões
- A regra dos relatórios diretos
- Definição da [precedência do operador](groups-dynamic-membership.md#operator-precedence)
- [Regras com expressões complexas;](groups-dynamic-membership.md#rules-with-complex-expressions) Por exemplo`(user.proxyAddresses -any (_ -contains "contoso"))`

> [!NOTE]
> O construtor de regras pode não ser capaz de exibir algumas regras construídas na caixa de texto. Pode ver uma mensagem quando o construtor de regras não é capaz de exibir a regra. O construtor de regras não altera de forma alguma a sintaxe suportada, validação ou processamento de regras dinâmicas do grupo.

Para obter mais instruções passo a passo, consulte [Criar ou atualizar um grupo dinâmico](groups-create-rule.md).

![Adicionar regra de adesão para um grupo dinâmico](./media/groups-dynamic-membership/update-dynamic-group-rule.png)

### <a name="rule-syntax-for-a-single-expression"></a>Sintaxe de regra para uma única expressão

Uma única expressão é a forma mais simples de uma regra de adesão e só tem as três partes acima mencionadas. Uma regra com uma única expressão `Property Operator Value`é semelhante a esta: , onde a sintaxe para a propriedade é o nome de objeto.propriedade.

Segue-se um exemplo de uma regra de adesão devidamente construída com uma única expressão:

```
user.department -eq "Sales"
```

Os parênteses são opcionais para uma única expressão. O comprimento total do corpo da sua regra de adesão não pode exceder 2048 caracteres.

## <a name="constructing-the-body-of-a-membership-rule"></a>Construindo o corpo de uma regra de adesão

Uma regra de adesão que povoa automaticamente um grupo com utilizadores ou dispositivos é uma expressão binária que resulta num resultado verdadeiro ou falso. As três partes de uma regra simples são:

- Propriedade
- Operador
- Valor

A ordem das peças dentro de uma expressão é importante para evitar erros de sintaxe.

## <a name="supported-properties"></a>Propriedades suportadas

Existem três tipos de propriedades que podem ser usadas para construir uma regra de adesão.

- Booleano
- String
- Coleção de cordas

Seguem-se as propriedades do utilizador que pode utilizar para criar uma única expressão.

### <a name="properties-of-type-boolean"></a>Propriedades do tipo boolean

| Propriedades | Valores permitidos | Utilização |
| --- | --- | --- |
| contaEnabled |verdadeiro falso |user.accountEnabled -eq true |
| dirSyncEnabled |verdadeiro falso |user.dirSyncEnabled -eq true |

### <a name="properties-of-type-string"></a>Propriedades da cadeia de tipo

| Propriedades | Valores permitidos | Utilização |
| --- | --- | --- |
| city |Qualquer valor de cadeia ou *nulo* |(user.city -eq "value") |
| país |Qualquer valor de cadeia ou *nulo* |(user.country -eq "value") |
| nome da empresa | Qualquer valor de cadeia ou *nulo* | (user.companyName -eq "value") |
| departamento |Qualquer valor de cadeia ou *nulo* |(user.department -eq "value") |
| displayName |Qualquer valor de cadeia |(user.displayName -eq "value") |
| funcionárioId |Qualquer valor de cadeia |(user.employeeId -eq "value")<br>(user.employeeId -ne *nulo)* |
| facsimilePhoneNumber |Qualquer valor de cadeia ou *nulo* |(user.facsimilePhoneNumber -eq "value") |
| nomeDado |Qualquer valor de cadeia ou *nulo* |(user.givenName -eq "value") |
| empregoTítulo |Qualquer valor de cadeia ou *nulo* |(user.jobTitle -eq "value") |
| correio |Qualquer valor de cadeia ou *nulo* (endereço SMTP do utilizador) |(user.mail -eq "value") |
| mailNickName |Qualquer valor de cadeia (pseudónimo de correio do utilizador) |(user.mailNickName -eq "value") |
| dispositivo móvel |Qualquer valor de cadeia ou *nulo* |(user.mobile -eq "value") |
| objectId |GUIA do objeto do utilizador |(user.objectId -eq "11111111-1111-1111-1111-1111-111111111111") |
| onPremisesSecurityIdentifier | Identificador de segurança no local (SID) para utilizadores que foram sincronizados desde as instalações até à nuvem. |(user.onPremisesSecurityIdentifier -eq "S-1-1-11-11111111-111111-1111111-111111111-11111111") |
| passwordPolíticas |Nenhum desactivapalavra-passe-passe desactivaavalidade depassworddesativação depassword, desactivapalavra-passe forte |(user.passwordPolicies -eq "DisableStrongPassword") |
| physicalDeliveryOfficeName |Qualquer valor de cadeia ou *nulo* |(user.physicalDeliveryOfficeName -eq "value") |
| Código postal |Qualquer valor de cadeia ou *nulo* |(user.postalCode -eq "value") |
| língua preferida |Código ISO 639-1 |(user.preferredLanguage -eq "en-US") |
| sipProxyAddress |Qualquer valor de cadeia ou *nulo* |(user.sipProxyAddress -eq "value") |
| state |Qualquer valor de cadeia ou *nulo* |(user.state -eq "value") |
| streetAddress |Qualquer valor de cadeia ou *nulo* |(user.streetAddress -eq "value") |
| surname |Qualquer valor de cadeia ou *nulo* |(user.surname -eq "value") |
| telefoneNúmero |Qualquer valor de cadeia ou *nulo* |(user.telephoneNumber -eq "value") |
| utilizaçãoLocalização |Dois código de país com letras |(user.usageLocation -eq "US") |
| userPrincipalName |Qualquer valor de cadeia |(user.userPrincipalName -eqalias@domain" ") |
| userType |membro convidado *nulo* |(user.userType -eq "Member") |

### <a name="properties-of-type-string-collection"></a>Propriedades da coleção de cordas tipo

| Propriedades | Valores permitidos | Utilização |
| --- | --- | --- |
| outros Mails |Qualquer valor de cadeia |(user.otherMails -contémalias@domain" |
| proxyAddresses |SMTP: alias@domain SMTP:alias@domain |(user.proxyAddresss -contém "SMTP: alias@domain") |

Para as propriedades utilizadas para as regras do dispositivo, consulte [Regras para dispositivos](#rules-for-devices).

## <a name="supported-expression-operators"></a>Operadores de expressão apoiadas

A tabela seguinte lista todos os operadores apoiados e a sua sintaxe para uma única expressão. Os operadores podem ser utilizados com ou sem o prefixo do hífen (-).

| Operador | Sintaxe |
| --- | --- |
| Não é igual |-ne |
| É igual a |-eq |
| Não começa com |-notStartsWith |
| Começa com |-começaCom |
| Não contém |-nãoContém |
| Contains |- contém |
| Não Corresponder |-notMatch |
| Match |-jogo |
| Entrada | - em |
| Não em | -notIn |

### <a name="using-the--in-and--notin-operators"></a>Utilização dos operadores -in e -notIn

Se pretender comparar o valor de um utilizador com uma série de valores diferentes, pode utilizar os operadores in -in ou -notIn. Utilize os símbolos do suporte "[" e "]" para iniciar e terminar a lista de valores.

 No exemplo seguinte, a expressão avalia a verdade se o valor do utilizador.departamento for igual a qualquer um dos valores da lista:

```
   user.department -in ["50001","50002","50003","50005","50006","50007","50008","50016","50020","50024","50038","50039","51100"]
```


### <a name="using-the--match-operator"></a>Usando o operador -match 
O operador **de correspondência** é utilizado para combinar qualquer expressão regular. Exemplos:

```
user.displayName -match "Da.*"   
```
Da, Dav, David avalia mato, a ADa avalia a falsa.

```
user.displayName -match ".*vid"
```
David avalia a verdade, Da avalia a falso.

## <a name="supported-values"></a>Valores suportados

Os valores utilizados numa expressão podem consistir em vários tipos, incluindo:

* Cadeias
* Boolean – verdadeiro, falso
* Números
* Arrays - array de números, matriz de cordas

Ao especificar um valor dentro de uma expressão, é importante utilizar a sintaxe correta para evitar erros. Algumas dicas de sintaxe são:

* As cotações duplas são opcionais a menos que o valor seja uma corda.
* As operações de cordas e regex não são sensíveis ao caso.
* Quando um valor de cadeia contém cotações duplas, \` ambas as cotações devem ser \`escapadas usando o personagem, por exemplo, user.department -eq "Sales"\`é a sintaxe adequada quando "Vendas" é o valor.
* Também pode efetuar controlos Nulos, `user.department -eq null`utilizando o nulo como valor, por exemplo, .

### <a name="use-of-null-values"></a>Utilização de valores nulos

Para especificar um valor nulo numa regra, pode utilizar o valor *nulo.* 

* Utilize -eq ou -ne ao comparar o valor *nulo* numa expressão.
* Utilize citações em torno da palavra *nula* apenas se quiser que seja interpretada como um valor literal de cordas.
* O operador não pode ser usado como um operador comparativo para nulo. Se o utilizar, obtém um erro quer utilize nuloou $null.

A forma correta de referência do valor nulo é a seguinte:

```
   user.mail –ne null
```

## <a name="rules-with-multiple-expressions"></a>Regras com múltiplas expressões

Uma regra de adesão ao grupo pode consistir em mais de uma expressão única ligada pelos operadores - e, ou - ou - não lógicos. Os operadores lógicos também podem ser utilizados em combinação. 

Seguem-se exemplos de regras de adesão devidamente construídas com múltiplas expressões:

```
(user.department -eq "Sales") -or (user.department -eq "Marketing")
(user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")
```

### <a name="operator-precedence"></a>Precedência de operadores

Todos os operadores estão listados abaixo por ordem de precedência do mais alto para o mais baixo. Os operadores da mesma linha são de igual precedência:

```
-eq -ne -startsWith -notStartsWith -contains -notContains -match –notMatch -in -notIn
-not
-and
-or
-any -all
```

Segue-se um exemplo de precedência do operador em que estão a ser avaliadas duas expressões para o utilizador:

```
   user.department –eq "Marketing" –and user.country –eq "US"
```

Os parênteses só são necessários quando a precedência não satisfaz os seus requisitos. Por exemplo, se quiser que o departamento seja avaliado primeiro, o seguinte mostra como os parênteses podem ser usados para determinar a ordem:

```
   user.country –eq "US" –and (user.department –eq "Marketing" –or user.department –eq "Sales")
```

## <a name="rules-with-complex-expressions"></a>Regras com expressões complexas

Uma regra de adesão pode consistir em expressões complexas onde os imóveis, operadores e valores assumem formas mais complexas. As expressões são consideradas complexas quando qualquer uma das seguintes palavras é verdadeira:

* O imóvel consiste numa coleção de valores; propriedades especificamente, multi-valorizadas
* As expressões usam os operadores - qualquer e -todos os operadores
* O valor da expressão pode ser, por si só, uma ou mais expressões

## <a name="multi-value-properties"></a>Propriedades de vários valores

Propriedades de vários valores são coleções de objetos do mesmo tipo. Podem ser utilizados para criar regras de adesão utilizando os operadores lógicos.

| Propriedades | Valores | Utilização |
| --- | --- | --- |
| planos atribuídos | Cada objeto da coleção expõe as seguintes propriedades de cordas: capabilityStatus, serviço, servicePlanId |user.designadoSPlanos -any (designadoPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -e designadoPlan.capabilityStatus -eq "Habilitado") |
| proxyAddresses| SMTP: alias@domain SMTP:alias@domain | (user.proxyAddresss -any\_ (-contém "contoso")) |

### <a name="using-the--any-and--all-operators"></a>Usando os operadores - qualquer e -todos os operadores

Pode utilizar - qualquer e -todos os operadores para aplicar uma condição a um ou todos os itens da coleção, respectivamente.

* -qualquer (satisfeito quando pelo menos um item na coleção corresponde à condição)
* -todos (satisfeitos quando todos os itens da coleção correspondem à condição)

#### <a name="example-1"></a>Exemplo 1

designadoSPlanos é uma propriedade de vários valores que lista todos os planos de serviço atribuídos ao utilizador. A seguinte expressão seleciona utilizadores que tenham o plano de serviço Exchange Online (Plan 2) (como valor GUID) que também se encontra em estado habilitado:

```
user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled")
```

Uma regra como esta pode ser usada para agrupar todos os utilizadores para os quais é ativada uma capacidade do Office 365 (ou outro Microsoft Online Service). Em seguida, pode candidatar-se a um conjunto de políticas para o grupo.

#### <a name="example-2"></a>Exemplo 2

A seguinte expressão seleciona todos os utilizadores que tenham qualquer plano de serviço associado ao serviço Intune (identificado pelo nome de serviço "SCO"):

```
user.assignedPlans -any (assignedPlan.service -eq "SCO" -and assignedPlan.capabilityStatus -eq "Enabled")
```

### <a name="using-the-underscore-_-syntax"></a>Usando a\_sintaxe de sublinhado

A sintaxe de sublinhado\_corresponde a ocorrências de um valor específico numa das propriedades de recolha de cordas multivalorizadas para adicionar utilizadores ou dispositivos a um grupo dinâmico. É utilizado com os operadores.

Aqui está um exemplo de\_usar o sublinhado ( ) numa regra para adicionar membros com base em user.proxyAddress (funciona o mesmo para user.otherMails). Esta regra adiciona qualquer utilizador com endereço proxy que contenha "contoso" ao grupo.

```
(user.proxyAddresses -any (_ -contains "contoso"))
```

## <a name="other-properties-and-common-rules"></a>Outras propriedades e regras comuns

### <a name="create-a-direct-reports-rule"></a>Criar uma regra de "Relatórios Diretos"

Pode criar um grupo que contenha todos os relatórios diretos de um gestor. Quando os relatórios diretos do gestor mudarem no futuro, a adesão do grupo é ajustada automaticamente.

A regra dos relatórios diretos é construída utilizando a seguinte sintaxe:

```
Direct Reports for "{objectID_of_manager}"
```

Aqui está um exemplo de uma regra válida em que "62e19b97-8b3d-4d4a-a106-4ce66896a863" é o objectid do gestor:

```
Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863"
```

As seguintes dicas podem ajudá-lo a usar a regra corretamente.

- O ID do **gerente** é a identificação do objeto do gerente. Pode ser encontrado no **perfil**do gerente.
- Para que a regra funcione, certifique-se de que a propriedade **do Gestor** está corretamente definida para os utilizadores da sua organização. Pode verificar o valor atual no **Perfil**do utilizador .
- Esta regra apoia apenas os relatórios diretos do gerente. Por outras palavras, não se pode criar um grupo com os relatórios diretos do gestor *e* os seus relatórios.
- Esta regra não pode ser combinada com outras regras de adesão.

### <a name="create-an-all-users-rule"></a>Criar uma regra "Todos os utilizadores"

Pode criar um grupo que contenha todos os utilizadores dentro de uma organização usando uma regra de adesão. Quando os utilizadores são adicionados ou removidos da organização no futuro, a adesão do grupo é ajustada automaticamente.

A regra "Todos os utilizadores" é construída utilizando uma única expressão utilizando o operador -ne e o valor nulo. Esta regra adiciona utilizadores convidados B2B, bem como utilizadores membros ao grupo.

```
user.objectId -ne null
```
Se quiser que o seu grupo exclua os utilizadores convidados e inclua apenas membros da sua organização, pode utilizar a seguinte sintaxe:

```
(user.objectId -ne null) -and (user.userType -eq "Member")
```

### <a name="create-an-all-devices-rule"></a>Criar uma regra "Todos os dispositivos"

Você pode criar um grupo que contenha todos os dispositivos dentro de uma organização usando uma regra de adesão. Quando os dispositivos são adicionados ou removidos da organização no futuro, a adesão do grupo é ajustada automaticamente.

A regra "Todos os Dispositivos" é construída utilizando uma única expressão utilizando o operador -ne e o valor nulo:

```
device.objectId -ne null
```

## <a name="extension-properties-and-custom-extension-properties"></a>Propriedades de extensão e propriedades de extensão personalizada

Os atributos de extensão e as propriedades de extensão personalizada são suportados como propriedades de cordas em regras dinâmicas de adesão. [Os atributos](https://docs.microsoft.com/graph/api/resources/onpremisesextensionattributes?view=graph-rest-1.0) de extensão são sincronizados a partir de anúncios do servidor de janela no local e tomam o formato de "ExtensionAttributeX", onde X é igual a 1 - 15. Aqui está um exemplo de uma regra que usa um atributo de extensão como propriedade:

```
(user.extensionAttribute15 -eq "Marketing")
```

As propriedades de [extensão personalizada](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-feature-directory-extensions) sincronizadas a partir de No local, o `user.extension_[GUID]_[Attribute]`Windows Server AD ou a partir de uma aplicação SaaS conectada e são do formato de, onde:

* [GUID] é o identificador único em Azure AD para a aplicação que criou o imóvel em Azure AD
* [Atributo] é o nome da propriedade tal como foi criado

Um exemplo de uma regra que usa uma propriedade de extensão personalizada é:

```
user.extension_c272a57b722d4eb29bfe327874ae79cb_OfficeNumber -eq "123"
```

O nome de propriedade personalizado pode ser encontrado no diretório consultando a propriedade de um utilizador usando o Graph Explorer e procurando o nome da propriedade. Além disso, pode agora selecionar o link de propriedades de **extensão personalizada** no dinâmico construtor de regras do grupo de utilizadores para introduzir um ID de aplicação único e receber a lista completa de propriedades de extensão personalizadas para usar ao criar uma regra de adesão dinâmica. Esta lista também pode ser atualizada para obter quaisquer novas propriedades de extensão personalizada para essa aplicação.

## <a name="rules-for-devices"></a>Regras para dispositivos

Também pode criar uma regra que seleciona objetos de dispositivo para adesão num grupo. Não pode ter utilizadores e dispositivos como membros do grupo. 

> [!NOTE]
> O atributo **organizacional da Unidade** já não está listado e não deve ser utilizado. Esta cadeia é definida pela Intune em casos específicos, mas não é reconhecida pela Azure AD, pelo que nenhum dispositivo é adicionado a grupos com base neste atributo.

> [!NOTE]
> os rótulos do sistema são um atributo só para leitura que não pode ser definido com Intune.
>
> Para o Windows 10, o formato correto do atributo do dispositivoOSVersão é o seguinte: (dispositivo.deviceOSVersion -eq "10.0.17763"). A formatação pode ser validada com o cmdlet Get-MsolDevice PowerShell.

Podem ser utilizados os seguintes atributos do dispositivo.

 Atributo do dispositivo  | Valores | Exemplo
 ----- | ----- | ----------------
 contaEnabled | verdadeiro falso | (dispositivo.accountEnabled -eq true)
 displayName | qualquer valor de cadeia |(dispositivo.displayName -eq "Rob iPhone")
 tipoDeSODoDispositivo | qualquer valor de cadeia | (dispositivo.deviceOSType -eq "iPad") -ou (dispositivo.deviceOSType -eq "iPhone")<br>(dispositivo.deviceOSType -contém "AndroidEnterprise")<br>(dispositivo.deviceOSType -eq "AndroidForWork")
 versãoDeSODoDispositivo | qualquer valor de cadeia | (dispositivo.deviceOSVersão -eq "9.1")
 dispositivoCategoria | um nome de categoria de dispositivo válido | (dispositivo.deviceCategoria -eq "BYOD")
 dispositivoFabricante | qualquer valor de cadeia | (dispositivo.deviceManufacturer -eq "Samsung")
 dispositivoModelo | qualquer valor de cadeia | (dispositivo.deviceModel -eq "iPad Air")
 dispositivoPropriedade | Pessoal, Empresa, Desconhecido | (dispositivo.deviceOwnership -eq "Empresa")
 nome perfil de inscrição | Perfil de inscrição de dispositivos apple, inscrição de dispositivos - identificadores de dispositivos corporativos (Android - Quiosque) ou nome do perfil do Windows Autopilot | (dispositivo.registrationProfileName -eq "DEP iPhones")
 isRooted | verdadeiro falso | (dispositivo.isRooted -eq true)
 gestãoType | MDM (para dispositivos móveis)<br>PC (para computadores geridos pelo agente intune PC) | (dispositivo.managementType -eq "MDM")
 deviceId | um id de dispositivo Azure AD válido | (dispositivo.deviceId -eq "d4fe7726-5966-431c-b3b8-cddc8fdb717d")
 objectId | um id de objeto azure válido |  (dispositivo.objectId -eq "76ad43c9-32c5-45e8-a272-7b58b58f596d")
 dispositivoPhysicalIds | qualquer valor de cadeia utilizado pelo Autopilot, tais como todos os dispositivos Autopilot, OrderID ou PurchaseOrderID  | (dispositivo.devicePhysicalIDs -any _ - contém "[ZTDId]") (dispositivo.devicePhysicalIds -any _ -eq "[OrderID]:179887111881") (dispositivo.devicePhysicalIds -any _ -eq "[PurchaseOrderId]:76222342342")
 sistemaRótulos | qualquer corda que corresponda à propriedade do dispositivo Intune para a marcação de dispositivos de local de trabalho modernos | (dispositivo.systemLabels -contém "M365Managed")

> [!Note]  
> Para o dispositivoPropriedade ao criar Grupos Dinâmicos para dispositivos é necessário definir o valor igual ao "Empresa". Em Intune, a propriedade do dispositivo é representada como Corporate. Consulte os [Proprietários](https://docs.microsoft.com/intune/reports-ref-devices#ownertypes) Para obter mais detalhes. 

## <a name="next-steps"></a>Passos seguintes

Estes artigos fornecem informações adicionais sobre grupos no Diretório Ativo Azure.

- [Ver grupos existentes](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Criar um novo grupo e adicionar membros](../fundamentals/active-directory-groups-create-azure-portal.md)
- [Gerir definições de um grupo](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Gerir associações de um grupo](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Gerir regras dinâmicas dos utilizadores num grupo](groups-create-rule.md)
