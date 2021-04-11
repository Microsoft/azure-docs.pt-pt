---
title: Regras para a adesão a grupos dinamicamente povoados - Azure AD | Microsoft Docs
description: Como criar regras de adesão para povoar automaticamente grupos, e uma referência de regra.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: overview
ms.date: 02/18/2021
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 41bdf5251881fa9307a3cd4d214081845e967900
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107209524"
---
# <a name="dynamic-membership-rules-for-groups-in-azure-active-directory"></a>Regras dinâmicas de adesão para grupos no Azure Ative Directory

No Azure Ative Directory (Azure AD), pode criar regras complexas baseadas em atributos para permitir adesões dinâmicas para grupos. A adesão ao grupo dinâmico reduz a sobrecarga administrativa de adicionar e remover os utilizadores. Este artigo detalha as propriedades e sintaxe para criar regras dinâmicas de adesão para utilizadores ou dispositivos. Pode configurar uma regra para a adesão dinâmica em grupos de segurança ou grupos Microsoft 365.

Quando quaisquer atributos de um utilizador ou dispositivo mudam, o sistema avalia todas as regras dinâmicas do grupo num diretório para ver se a alteração desencadearia qualquer grupo que adicionasse ou removesse. Se um utilizador ou dispositivo satisfaz uma regra de um grupo, são adicionados como membro desse grupo. Se já não satisfazem a regra, são removidos. Não é possível adicionar ou remover manualmente um membro de um grupo dinâmico.

- Pode criar um grupo dinâmico para dispositivos ou para utilizadores, mas não é possível criar uma regra que contenha tanto os utilizadores como os dispositivos.
- Não é possível criar um grupo de dispositivos baseado nos atributos dos proprietários do dispositivo. As regras de adesão ao dispositivo só podem fazer referência aos atributos do dispositivo.

> [!NOTE]
> Esta funcionalidade requer uma licença Azure AD Premium P1 para cada utilizador único que seja membro de um ou mais grupos dinâmicos. Não é necessário atribuir licenças aos utilizadores para que sejam membros de grupos dinâmicos, mas deve ter o número mínimo de licenças na organização AZure AD para cobrir todos esses utilizadores. Por exemplo, se tivesse um total de 1.000 utilizadores únicos em todos os grupos dinâmicos da sua organização, precisaria de pelo menos 1.000 licenças para o Azure AD Premium P1 satisfazer o requisito da licença.
> Não é necessária qualquer licença para dispositivos que sejam membros de um grupo de dispositivos dinâmicos.

## <a name="rule-builder-in-the-azure-portal"></a>Construtor de regras no portal Azure

A Azure AD fornece um construtor de regras para criar e atualizar as suas regras importantes mais rapidamente. O construtor de regras apoia a construção de até cinco expressões. O construtor de regras torna mais fácil formar uma regra com algumas expressões simples, no entanto, não pode ser usado para reproduzir todas as regras. Se o construtor de regras não apoiar a regra que pretende criar, pode utilizar a caixa de texto.

Aqui estão alguns exemplos de regras avançadas ou sintaxe para as quais recomendamos que construa usando a caixa de texto:

- Governar com mais de cinco expressões
- A regra dos relatórios diretos
- Definição [da precedência do operador](#operator-precedence)
- [Regras com expressões complexas;](#rules-with-complex-expressions) Por exemplo `(user.proxyAddresses -any (_ -contains "contoso"))`

> [!NOTE]
> O construtor de regras pode não ser capaz de exibir algumas regras construídas na caixa de texto. Pode ver uma mensagem quando o construtor de regras não é capaz de mostrar a regra. O construtor de regras não altera a sintaxe, validação ou processamento de regras dinâmicas do grupo de qualquer forma.

Para obter mais instruções passo a passo, consulte [Criar ou atualizar um grupo dinâmico](groups-create-rule.md).

![Adicionar regra de adesão para um grupo dinâmico](./media/groups-dynamic-membership/update-dynamic-group-rule.png)

### <a name="rule-syntax-for-a-single-expression&quot;></a>Sintaxe de regra para uma única expressão

Uma única expressão é a forma mais simples de uma regra de adesão e tem apenas as três partes acima mencionadas. Uma regra com uma única expressão é semelhante a esta: `Property Operator Value` , onde a sintaxe para a propriedade é o nome de object.property.

Segue-se um exemplo de uma regra de adesão devidamente construída com uma única expressão:

```
user.department -eq &quot;Sales&quot;
```

Os parênteses são opcionais para uma única expressão. O comprimento total do corpo da sua regra de adesão não pode exceder 3072 caracteres.

## <a name=&quot;constructing-the-body-of-a-membership-rule&quot;></a>Construção do corpo de uma regra de adesão

Uma regra de adesão que povoa automaticamente um grupo com utilizadores ou dispositivos é uma expressão binária que resulta num resultado verdadeiro ou falso. As três partes de uma regra simples são:

- Propriedade
- Operador
- Valor

A ordem das peças dentro de uma expressão é importante para evitar erros de sintaxe.

## <a name=&quot;supported-properties&quot;></a>Propriedades suportadas

Existem três tipos de propriedades que podem ser usadas para construir uma regra de adesão.

- Booleano
- Cadeia
- Coleção de cordas

Seguem-se as propriedades do utilizador que pode utilizar para criar uma única expressão.

### <a name=&quot;properties-of-type-boolean&quot;></a>Propriedades do tipo boolean

| Propriedades | Valores permitidos | Utilização |
| --- | --- | --- |
| accountEnabled |verdadeiro falso |user.accountEnabled -eq verdadeiro |
| dirSyncEnabled |verdadeiro falso |user.dirSyncEnabled -eq verdadeiro |

### <a name=&quot;properties-of-type-string&quot;></a>Propriedades de cadeia de tipo

| Propriedades | Valores permitidos | Utilização |
| --- | --- | --- |
| city |Qualquer valor de corda ou *nulo* |(user.city -eq &quot;value") |
| país |Qualquer valor de corda ou *nulo* |(user.country -eq "value") |
| nome da empresa | Qualquer valor de corda ou *nulo* | (user.companyName -eq "valor") |
| departamento |Qualquer valor de corda ou *nulo* |(user.department -eq "value") |
| displayName |Qualquer valor de corda |(user.displayName -eq "valor") |
| employeeId |Qualquer valor de corda |(user.employeeId -eq "valor")<br>(user.employeeId -ne *null)* |
| facsimileTelephoneNumber |Qualquer valor de corda ou *nulo* |(user.facsimileTelephoneNumber -eq "value") |
| nomeDado |Qualquer valor de corda ou *nulo* |(user.givenName -eq "valor") |
| jobTitle |Qualquer valor de corda ou *nulo* |(user.jobTit -eq "value") |
| correio |Qualquer valor de cadeia ou *nulo* (endereço SMTP do utilizador) |(user.mail -eq "valor") |
| mailNickName |Qualquer valor de cadeia (pseudónimo de correio do utilizador) |(user.mailNickName -eq "valor") |
| dispositivo móvel |Qualquer valor de corda ou *nulo* |(user.mobile -eq "valor") |
| objectId |GUIA do objeto do utilizador |(user.objectId -eq "11111111-1111-1111-1111-1111-11111111111111") |
| onPremisesSecurityIdentifier | Identificador de segurança no local (SID) para utilizadores que foram sincronizados desde as instalações até à nuvem. |(user.onPremisesSecurityIdentifier -eq "S-1-1-11-11111111111-11111111-1111111-1111111-1111111") |
| passwordPolícias |Nenhum desativar a palavra-passe desactivarpassapassaxxexpirationPasswordExpiration, DisableStrongPassword |(user.passwordPolicies -eq "DisableStrongPassword") |
| physicalDeliveryOfficeName |Qualquer valor de corda ou *nulo* |(user.physicalDeliveryOfficeName -eq "value") |
| postalCode |Qualquer valor de corda ou *nulo* |(user.postalCode -eq "valor") |
| preferiuLanguage |Código ISO 639-1 |(user.preferredLanguage -eq "en-US") |
| sipProxyAddress |Qualquer valor de corda ou *nulo* |(user.sipProxyAddress -eq "value") |
| state |Qualquer valor de corda ou *nulo* |(user.state-eq "valor") |
| streetAddress |Qualquer valor de corda ou *nulo* |(user.streetAddress -eq "valor") |
| surname |Qualquer valor de corda ou *nulo* |(user.sobrenome -eq "valor") |
| telephoneNumber |Qualquer valor de corda ou *nulo* |(user.telephoneNumber -eq "valor") |
| usageLocation |Dois códigos de país/região com letras |(user.usageLocation -eq "US") |
| userPrincipalName |Qualquer valor de corda |(user.userPrincipalName -eq " alias@domain ") |
| userType |membro convidado *nulo* |(user.userType -eq "Member") |

### <a name="properties-of-type-string-collection"></a>Propriedades da coleção de cordas tipo

| Propriedades | Valores permitidos | Utilização |
| --- | --- | --- |
| outras Mensagens |Qualquer valor de corda |(user.otherMails -contém alias@domain " " |
| proxyAddresses |SMTP: alias@domain smtp: alias@domain |(user.proxyAddresses -contém "SMTP: alias@domain "" |

Para as propriedades utilizadas para as regras do dispositivo, consulte [regras para dispositivos](#rules-for-devices).

## <a name="supported-expression-operators"></a>Operadores de expressão apoiados

A tabela que se segue lista todos os operadores apoiados e a sua sintaxe para uma única expressão. Os operadores podem ser utilizados com ou sem o prefixo hífen (-) prefixo.

| Operador | Syntax |
| --- | --- |
| Não é igual |-ne |
| Igual a |-eq |
| Não começa com |-notStartsWith |
| Começa com |-começa Com |
| Não contém |-nãoContóis |
| Contains |-contém |
| Não corresponder |-não-Mese-ver |
| Match |-combinar |
| Em | -em |
| Não em | -nãoIn |

### <a name="using-the--in-and--notin-operators"></a>Utilizando os operadores -in e -notIn

Se quiser comparar o valor de um atributo de utilizador com uma série de valores diferentes, pode utilizar os operadores -in ou -notIn. Utilize os símbolos do suporte "[" e "]" para iniciar e terminar a lista de valores.

 No exemplo seguinte, a expressão avalia a verdade se o valor do utilizador.departamento é igual a qualquer um dos valores da lista:

```
   user.department -in ["50001","50002","50003","50005","50006","50007","50008","50016","50020","50024","50038","50039","51100"]
```


### <a name="using-the--match-operator"></a>Utilizando o operador de correspondência 
O operador **de correspondência** é utilizado para corresponder a qualquer expressão regular. Exemplos:

```
user.displayName -match "Da.*"   
```
Da, Dav, David avaliam a verdade, a ADa avalia como falso.

```
user.displayName -match ".*vid"
```
David avalia a verdade, Da avalia a falsa.

## <a name="supported-values"></a>Valores suportados

Os valores utilizados numa expressão podem consistir em vários tipos, incluindo:

* Cadeias
* Booleano - verdadeiro, falso
* Números
* Arrays - matriz de números, matriz de cordas

Ao especificar um valor dentro de uma expressão, é importante utilizar a sintaxe correta para evitar erros. Algumas dicas de sintaxe são:

* As cotações duplas são opcionais, a menos que o valor seja uma corda.
* As operações de cordas e regex não são sensíveis a casos.
* Quando um valor de cadeia contém cotações duplas, ambas as cotações devem ser escapadas usando o \` personagem, por exemplo, user.department -eq \` \` "Sales" é a sintaxe adequada quando "Vendas" é o valor.
* Também pode efetuar verificações nulas, utilizando nulos como valor, por exemplo, `user.department -eq null` .

### <a name="use-of-null-values"></a>Utilização de valores nulos

Para especificar um valor nulo numa regra, pode utilizar o valor *nulo.* 

* Utilize -eq ou -ne ao comparar o valor *nulo* numa expressão.
* Utilize aspas em torno da palavra *nulo* apenas se quiser que seja interpretada como um valor de corda literal.
* O operador não pode ser usado como um operador comparativo para nulo. Se o utilizar, terá um erro quer utilize nulo ou $null.

A forma correta de referenciar o valor nulo é a seguinte:

```
   user.mail –ne null
```

## <a name="rules-with-multiple-expressions"></a>Regras com múltiplas expressões

Uma regra de adesão ao grupo pode consistir em mais de uma única expressão ligada pelos operadores -e, ou não lógicos. Os operadores lógicos também podem ser usados em combinação. 

Seguem-se exemplos de regras de adesão devidamente construídas com múltiplas expressões:

```
(user.department -eq "Sales") -or (user.department -eq "Marketing")
(user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")
```

### <a name="operator-precedence"></a>Precedência de operadores

Todos os operadores estão listados abaixo por ordem de precedência do mais alto para o mais baixo. Os operadores na mesma linha têm igual precedência:

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

Os parênteses só são necessários quando a precedência não satisfaz os seus requisitos. Por exemplo, se pretender que o departamento seja avaliado primeiro, o seguinte mostra como os parênteses podem ser usados para determinar a ordem:

```
   user.country –eq "US" –and (user.department –eq "Marketing" –or user.department –eq "Sales")
```

## <a name="rules-with-complex-expressions"></a>Regras com expressões complexas

Uma regra de adesão pode consistir em expressões complexas onde as propriedades, operadores e valores assumem formas mais complexas. As expressões são consideradas complexas quando qualquer uma das seguintes são verdadeiras:

* O imóvel é constituído por uma coleção de valores; especificamente, propriedades multi-valor
* As expressões usam os operadores -todos e todos os operadores
* O valor da expressão pode ser uma ou mais expressões

## <a name="multi-value-properties"></a>Propriedades multi-valor

Propriedades multi-valor são coleções de objetos do mesmo tipo. Podem ser usados para criar regras de adesão utilizando os operadores lógicos de todos e quaisquer operadores lógicos.

| Propriedades | Valores | Utilização |
| --- | --- | --- |
| planos atribuídos | Cada objeto da coleção expõe as seguintes propriedades de cordas: capabilityStatus, serviço, servicePlanId |user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -e designado Plano.capabilityStatus -eq "Enabled") |
| proxyAddresses| SMTP: alias@domain smtp: alias@domain | (user.proxyAddresses -any \_ (-contém "contoso")) |

### <a name="using-the--any-and--all-operators"></a>Usando os -todos e todos os operadores

Pode utilizar -todos e todos os operadores para aplicar uma condição a um ou todos os itens da coleção, respectivamente.

* -qualquer (satisfeito quando pelo menos um item da recolha corresponde à condição)
* -todos (satisfeitos quando todos os itens da recolha correspondem à condição)

#### <a name="example-1"></a>Exemplo 1

a assignedPlans é uma propriedade multi-valor que lista todos os planos de serviço atribuídos ao utilizador. A expressão a seguir seleciona os utilizadores que tenham o plano de serviço Exchange Online (Plan 2) (como um valor GUID) que também está em estado habilitado:

```
user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled")
```

Uma regra como esta pode ser usada para agrupar todos os utilizadores para os quais está ativada uma capacidade microsoft 365 (ou outro Microsoft Online Service). Pode então aplicar-se com um conjunto de políticas ao grupo.

#### <a name="example-2"></a>Exemplo 2

A expressão a seguir seleciona todos os utilizadores que tenham qualquer plano de serviço associado ao serviço Intune (identificado pelo nome de serviço "SCO"):

```
user.assignedPlans -any (assignedPlan.service -eq "SCO" -and assignedPlan.capabilityStatus -eq "Enabled")
```

#### <a name="example-3"></a>Exemplo 3

A expressão a seguir seleciona todos os utilizadores que não tenham um plano de serviço assinado:

```
user.assignedPlans -all (assignedPlan.servicePlanId -eq "")
```

### <a name="using-the-underscore-_-syntax"></a>Utilizando a sintaxe sublinhada ( \_ )

A \_ sintaxe sublinhada corresponde a ocorrências de um valor específico numa das propriedades multivalorizadas de recolha de cordas para adicionar utilizadores ou dispositivos a um grupo dinâmico. É utilizado com todos os operadores.

Aqui está um exemplo de utilização do sublinhado ( \_ ) numa regra para adicionar membros com base no user.proxyAddress (funciona da mesma forma para o utilizador.otherMails). Esta regra adiciona qualquer utilizador com endereço de procuração que contenha "contoso" ao grupo.

```
(user.proxyAddresses -any (_ -contains "contoso"))
```

## <a name="other-properties-and-common-rules"></a>Outras propriedades e regras comuns

### <a name="create-a-direct-reports-rule"></a>Criar uma regra de "relatórios diretos"

Pode criar um grupo que contenha todos os relatórios diretos de um gestor. Quando os relatórios diretos do gestor mudam no futuro, a adesão do grupo é ajustada automaticamente.

A regra dos relatórios diretos é construída utilizando a seguinte sintaxe:

```
Direct Reports for "{objectID_of_manager}"
```

Aqui está um exemplo de uma regra válida onde "62e19b97-8b3d-4d4a-a106-4ce66896a863" é o objetoID do gestor:

```
Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863"
```

As seguintes dicas podem ajudá-lo a utilizar a regra corretamente.

- A **identificação do gerente** é a identificação do objeto do gerente. Pode ser encontrado no **Perfil** do gestor.
- Para que a regra funcione, certifique-se de que a propriedade **manager** está corretamente definida para os utilizadores da sua organização. Pode verificar o valor atual no **Perfil** do utilizador.
- Esta regra apoia apenas os relatórios diretos do gestor. Por outras palavras, não se pode criar um grupo com os relatórios diretos do gestor *e* os seus relatórios.
- Esta regra não pode ser combinada com quaisquer outras regras de adesão.

### <a name="create-an-all-users-rule"></a>Criar uma regra "Todos os utilizadores"

Pode criar um grupo que contenha todos os utilizadores dentro de uma organização utilizando uma regra de adesão. Quando os utilizadores são adicionados ou removidos da organização no futuro, a adesão do grupo é ajustada automaticamente.

A regra "Todos os utilizadores" é construída com uma única expressão utilizando o operador -ne e o valor nulo. Esta regra adiciona utilizadores convidados B2B, bem como utilizadores membros ao grupo.

```
user.objectId -ne null
```
Se pretender que o seu grupo exclua os utilizadores convidados e inclua apenas membros da sua organização, pode utilizar a seguinte sintaxe:

```
(user.objectId -ne null) -and (user.userType -eq "Member")
```

### <a name="create-an-all-devices-rule"></a>Criar uma regra "Todos os dispositivos"

Pode criar um grupo que contenha todos os dispositivos dentro de uma organização usando uma regra de adesão. Quando os dispositivos são adicionados ou removidos da organização no futuro, a adesão do grupo é ajustada automaticamente.

A regra "Todos os Dispositivos" é construída com uma única expressão utilizando o operador -ne e o valor nulo:

```
device.objectId -ne null
```

## <a name="extension-properties-and-custom-extension-properties"></a>Propriedades de extensão e propriedades de extensão personalizadas

Os atributos de extensão e as propriedades de extensão personalizadas são suportados como propriedades de cordas em regras dinâmicas de adesão. [Os atributos](/graph/api/resources/onpremisesextensionattributes) de extensão são sincronizados a partir do AD do Servidor de Janela no local e tomam o formato de "ExtensionAttributeX", onde X é igual a 1 - 15. Aqui está um exemplo de uma regra que usa um atributo de extensão como propriedade:

```
(user.extensionAttribute15 -eq "Marketing")
```

[As propriedades de extensão personalizada](../hybrid/how-to-connect-sync-feature-directory-extensions.md) são sincronizadas a partir de AD do Windows Server ou de uma aplicação SaaS conectada e são do formato de `user.extension_[GUID]_[Attribute]` , onde:

* [GUID] é o identificador único em Azure AD para a aplicação que criou o imóvel em Azure AD
* [Atributo] é o nome da propriedade tal como foi criado

Um exemplo de uma regra que usa uma propriedade de extensão personalizada é:

```
user.extension_c272a57b722d4eb29bfe327874ae79cb_OfficeNumber -eq "123"
```

O nome da propriedade personalizada pode ser encontrado no diretório consultando a propriedade de um utilizador usando o Graph Explorer e procurando o nome da propriedade. Além disso, pode agora selecionar Obter o link **de propriedades de extensão personalizada** no dinâmico construtor de regras do grupo de utilizadores para introduzir um ID de aplicação único e receber a lista completa de propriedades de extensão personalizadas para usar ao criar uma regra de adesão dinâmica. Esta lista também pode ser atualizada para obter quaisquer novas propriedades de extensão personalizada para essa aplicação.

## <a name="rules-for-devices"></a>Regras para dispositivos

Também pode criar uma regra que selecione objetos de dispositivo para a adesão a um grupo. Não pode ter tanto os utilizadores como os dispositivos como membros do grupo. 

> [!NOTE]
> O atributo **organizationalUnit** já não está listado e não deve ser utilizado. Esta cadeia é definida pela Intune em casos específicos, mas não é reconhecida pela Azure AD, pelo que nenhum dispositivo é adicionado a grupos com base neste atributo.

> [!NOTE]
> systemlabels é um atributo apenas de leitura que não pode ser definido com Intune.
>
> Para o Windows 10, o formato correto do atributo dispositivoOSVersion é o seguinte: (dispositivo.deviceOSVersion -eq "10.0.17763"). A formatação pode ser validada com o Get-MsolDevice cmdlet PowerShell.

Podem ser utilizados os seguintes atributos do dispositivo.

 Atributo do dispositivo  | Valores | Exemplo
 ----- | ----- | ----------------
 accountEnabled | verdadeiro falso | (device.accountEnabled -eq true)
 displayName | qualquer valor de cadeia |(dispositivo.displayName -eq "Rob iPhone")
 tipoDeSODoDispositivo | qualquer valor de cadeia | (dispositivo.deviceOSType -eq "iPad") - ou (dispositivo.deviceOSType -eq "iPhone")<br>(dispositivo.deviceOSType -contém "AndroidEnterprise")<br>(dispositivo.deviceOSType -eq "AndroidForWork")<br>(dispositivo.deviceOSType -eq "Windows")
 versãoDeSODoDispositivo | qualquer valor de cadeia | (dispositivo.deviceOSVersion -eq "9.1")<br>(dispositivo.deviceOSVersion -eq "10.0.17763.0")
 dispositivoCategoria | um nome válido da categoria de dispositivo | (dispositivo.deviceCategoria -eq "BYOD")
 dispositivo Manfacturer | qualquer valor de cadeia | (dispositivo.deviceManufacturer -eq "Samsung")
 modelo de dispositivo | qualquer valor de cadeia | (dispositivo.deviceModel -eq "iPad Air")
 deviceOwnership | Pessoal, Companhia, Desconhecido | (dispositivo.deviceOwnership -eq "Empresa")
 inscriçãoProfileName | Nome do perfil de inscrição de dispositivo apple, nome de perfil de inscrição de dispositivo dedicado da Empresa Android Enterprise ou nome do perfil do Windows Autopilot | (device.registrationProfileName -eq "DEP iPhones")
 isRooted | verdadeiro falso | (dispositivo.isRooted-eq verdadeiro)
 tipo de gestão | MDM (para dispositivos móveis)<br>PC (para computadores geridos pelo agente intune PC) | (dispositivo.managementType -eq "MDM")
 deviceId | um ID de dispositivo AZure AD válido | (dispositivo.deviceId -eq "d4fe7726-5966-431c-b3b8-cddc8fdb717d")
 objectId | um ID de objeto AZure válido |  (dispositivo.objectId -eq "76ad43c9-32c5-45e8-a272-7b58b58f596d")
 dispositivosFÍsicos | qualquer valor de cadeia utilizado pelo Autopilot, como todos os dispositivos Autopilot, OrderID ou PurchaseOrderID  | (dispositivo.devicePhysicalIDs -any _ -contém "[ZTDId]") (dispositivo.devicePhysicalIds -any _ -eq "[OrderID]:179887111881") (dispositivo.deviceFísicos -any _ -eq "[PurchaseOrderId]:76222342342")
 systemLabels | qualquer cadeia que combine a propriedade do dispositivo Intune para marcar dispositivos do Local de Trabalho Moderno | (device.systemLabels -contém "M365Managed")

> [!Note]  
> Para o dispositivoSaonerar ao criar Grupos Dinâmicos para dispositivos, é necessário definir o valor igual a "Empresa". No Intune, a propriedade do dispositivo é representada como Corporate. Consulte os [Tipos Proprietários](/intune/reports-ref-devices#ownertypes) para obter mais detalhes. 

## <a name="next-steps"></a>Passos seguintes

Estes artigos fornecem informações adicionais sobre grupos no Diretório Ativo Azure.

- [Ver grupos existentes](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Criar um novo grupo e adicionar membros](../fundamentals/active-directory-groups-create-azure-portal.md)
- [Gerir definições de um grupo](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Gerir associações de um grupo](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Gerir regras dinâmicas dos utilizadores num grupo](groups-create-rule.md)
