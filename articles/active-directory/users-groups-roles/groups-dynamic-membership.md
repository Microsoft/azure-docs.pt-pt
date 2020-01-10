---
title: Regras para associação de grupo dinâmico-Azure AD | Microsoft Docs
description: Como criar regras de associação para preencher automaticamente os grupos e uma referência de regra.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/27/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f5be34a58d8f0416a31cd575ef0fea614b3d43e
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75768724"
---
# <a name="dynamic-membership-rules-for-groups-in-azure-active-directory"></a>Regras de associação dinâmica para grupos no Azure Active Directory

No Azure Active Directory (AD do Azure), você pode criar regras complexas baseadas em atributos para habilitar associações dinâmicas para grupos. A associação de grupo dinâmico reduz a sobrecarga administrativa de adicionar e remover usuários. Este artigo detalha as propriedades e a sintaxe para criar regras de associação dinâmica para usuários ou dispositivos. Pode configurar uma regra de filiação dinâmica em grupos de segurança ou grupos do Office 365.

Quando qualquer atributo de um usuário ou dispositivo é alterado, o sistema avalia todas as regras de grupo dinâmicas em um diretório para ver se a alteração dispararia qualquer adição ou remoção de grupo. Se um usuário ou dispositivo satisfizer uma regra em um grupo, eles serão adicionados como um membro desse grupo. Se eles não atenderem mais à regra, eles serão removidos. Você não pode adicionar ou remover manualmente um membro de um grupo dinâmico.

- Você pode criar um grupo dinâmico para dispositivos ou para usuários, mas não pode criar uma regra que contenha usuários e dispositivos.
- Não é possível criar um grupo de dispositivos com base nos atributos dos proprietários dos dispositivos. As regras de associação de dispositivo só podem referenciar atributos de dispositivo.

> [!NOTE]
> Esse recurso requer uma licença Azure AD Premium P1 para cada usuário exclusivo que seja membro de um ou mais grupos dinâmicos. Você não precisa atribuir licenças aos usuários para que eles sejam membros de grupos dinâmicos, mas você deve ter o número mínimo de licenças no locatário para cobrir todos esses usuários. Por exemplo, se você tivesse um total de 1.000 usuários exclusivos em todos os grupos dinâmicos em seu locatário, precisaria de pelo menos uma licença de 1.000 para Azure AD Premium P1 para atender ao requisito de licença.
> Nenhuma licença é necessária para dispositivos que são membros de um grupo de dispositivos dinâmico.

## <a name="rule-builder-in-the-azure-portal"></a>Construtor de regras no portal do Azure

O Azure AD fornece um construtor de regras para criar e atualizar suas regras importantes mais rapidamente. O construtor de regras dá suporte à construção de até cinco expressões. O construtor de regras torna mais fácil formar uma regra com algumas expressões simples, no entanto, ela não pode ser usada para reproduzir todas as regras. Se o construtor de regras não oferecer suporte à regra que você deseja criar, você poderá usar a caixa de texto.

Aqui estão alguns exemplos de regras avançadas ou sintaxe para as quais recomendamos que você construa usando a caixa de texto:

- Regra com mais de cinco expressões
- A regra de relatórios diretos
- Definindo a [precedência de operador](groups-dynamic-membership.md#operator-precedence)
- [Regras com expressões complexas](groups-dynamic-membership.md#rules-with-complex-expressions); por exemplo `(user.proxyAddresses -any (_ -contains "contoso"))`

> [!NOTE]
> O construtor de regras pode não ser capaz de exibir algumas regras construídas na caixa de texto. Você poderá ver uma mensagem quando o construtor de regras não puder exibir a regra. O construtor de regras não altera a sintaxe com suporte, a validação nem o processamento de regras de grupo dinâmicas de forma alguma.

Para obter instruções passo a passo, consulte [criar ou atualizar um grupo dinâmico](groups-create-rule.md).

![Adicionar regra de associação a um grupo dinâmico](./media/groups-dynamic-membership/update-dynamic-group-rule.png)

### <a name="rule-syntax-for-a-single-expression"></a>Sintaxe de regra para uma única expressão

Uma única expressão é a forma mais simples de uma regra de associação e só tem as três partes mencionadas acima. Uma regra com uma única expressão é semelhante a esta: `Property Operator Value`, em que a sintaxe da propriedade é o nome de Object. Property.

Veja a seguir um exemplo de uma regra de associação construída corretamente com uma única expressão:

```
user.department -eq "Sales"
```

Os parênteses são opcionais para uma única expressão. O comprimento total do corpo da sua regra de associação não pode exceder 2048 caracteres.

## <a name="constructing-the-body-of-a-membership-rule"></a>Construindo o corpo de uma regra de associação

Uma regra de associação que preenche automaticamente um grupo com usuários ou dispositivos é uma expressão binária que resulta em um resultado verdadeiro ou falso. As três partes de uma regra simples são:

- Propriedade
- Operador
- Valor

A ordem das partes dentro de uma expressão é importante para evitar erros de sintaxe.

## <a name="supported-properties"></a>Propriedades com suporte

Há três tipos de propriedades que podem ser usadas para construir uma regra de associação.

- Booleano
- String
- Coleção de cadeias de caracteres

A seguir estão as propriedades do usuário que você pode usar para criar uma única expressão.

### <a name="properties-of-type-boolean"></a>Propriedades do tipo booliano

| Propriedades | Valores permitidos | Utilização |
| --- | --- | --- |
| accountEnabled |verdadeiro falso |User. accountEnabled-EQ true |
| dirSyncEnabled |verdadeiro falso |User. dirSyncEnabled-EQ true |

### <a name="properties-of-type-string"></a>Propriedades do tipo cadeia de caracteres

| Propriedades | Valores permitidos | Utilização |
| --- | --- | --- |
| city |Qualquer valor de cadeia de caracteres ou *nulo* |(User. City-eq "valor") |
| país |Qualquer valor de cadeia de caracteres ou *nulo* |(User. Country-EQ "valor") |
| companyName | Qualquer valor de cadeia de caracteres ou *nulo* | (user.companyName -eq "value") |
| Departamento |Qualquer valor de cadeia de caracteres ou *nulo* |(User. Department-EQ "valor") |
| displayName |Qualquer valor de cadeia de caracteres |(user.displayName -eq "value") |
| employeeId |Qualquer valor de cadeia de caracteres |(user.employeeId -eq "value")<br>(User. employeeId-ne *NULL*) |
| facsimileTelephoneNumber |Qualquer valor de cadeia de caracteres ou *nulo* |(user.facsimileTelephoneNumber -eq "value") |
| givenName |Qualquer valor de cadeia de caracteres ou *nulo* |(user.givenName -eq "value") |
| jobTitle |Qualquer valor de cadeia de caracteres ou *nulo* |(User. jobTitle-EQ "valor") |
| correio |Qualquer valor de cadeia de caracteres ou *nulo* (endereço SMTP do usuário) |(User. mail-EQ "valor") |
| mailNickName |Qualquer valor de cadeia de caracteres (alias de email do usuário) |(user.mailNickName -eq "value") |
| móvel |Qualquer valor de cadeia de caracteres ou *nulo* |(user.mobile -eq "value") |
| objectId |GUID do objeto de usuário |(user.objectId -eq "11111111-1111-1111-1111-111111111111") |
| onPremisesSecurityIdentifier | SID (identificador de segurança) local para usuários que foram sincronizados do local para a nuvem. |(user.onPremisesSecurityIdentifier -eq "S-1-1-11-1111111111-1111111111-1111111111-1111111") |
| passwordPolicies |Nenhum DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration, DisableStrongPassword |(user.passwordPolicies -eq "DisableStrongPassword") |
| physicalDeliveryOfficeName |Qualquer valor de cadeia de caracteres ou *nulo* |(user.physicalDeliveryOfficeName -eq "value") |
| postalCode |Qualquer valor de cadeia de caracteres ou *nulo* |(user.postalCode -eq "value") |
| preferredLanguage |Código ISO 639-1 |(User. preferredLanguage-EQ "en-US") |
| sipProxyAddress |Qualquer valor de cadeia de caracteres ou *nulo* |(user.sipProxyAddress -eq "value") |
| state |Qualquer valor de cadeia de caracteres ou *nulo* |(User. State-EQ "valor") |
| streetAddress |Qualquer valor de cadeia de caracteres ou *nulo* |(user.streetAddress -eq "value") |
| Apelido |Qualquer valor de cadeia de caracteres ou *nulo* |(User. sobrenome-EQ "valor") |
| telephoneNumber |Qualquer valor de cadeia de caracteres ou *nulo* |(User. telephoneNumber-EQ "valor") |
| usageLocation |Código de país de duas letras |(user.usageLocation -eq "US") |
| userPrincipalName |Qualquer valor de cadeia de caracteres |(user.userPrincipalName -eq "alias@domain") |
| userType |membro convidado *nulo* |(User. UserType-EQ "membro") |

### <a name="properties-of-type-string-collection"></a>Propriedades do tipo coleção de cadeia de caracteres

| Propriedades | Valores permitidos | Utilização |
| --- | --- | --- |
| otherMails |Qualquer valor de cadeia de caracteres |(User. otherMails-contém "alias@domain") |
| proxyAddresses |SMTP: alias@domain SMTP: alias@domain |(User. proxyAddresses-contém "SMTP: alias@domain") |

Para as propriedades usadas para regras de dispositivo, consulte [regras para dispositivos](#rules-for-devices).

## <a name="supported-expression-operators"></a>Operadores de expressão com suporte

A tabela a seguir lista todos os operadores com suporte e sua sintaxe para uma única expressão. Os operadores podem ser usados com ou sem o prefixo de hífen (-).

| Operador | Sintaxe |
| --- | --- |
| Não é igual a |-ne |
| Igual a |-eq |
| Não começa com |-notStartsWith |
| Começa com |-startsWith |
| Não contém |-notContains |
| Contains |-contains |
| Sem correspondência |-notMatch |
| Correspondência |-correspondência |
| Em | -in |
| Não em | -notIn |

### <a name="using-the--in-and--notin-operators"></a>Usando os operadores-in e-notIn

Se você quiser comparar o valor de um atributo de usuário com um número de valores diferentes, poderá usar os operadores-in ou-notIn. Use os símbolos de colchetes "[" e "]" para começar e terminar a lista de valores.

 No exemplo a seguir, a expressão será avaliada como true se o valor de User. Department for igual a qualquer um dos valores na lista:

```
   user.department -in ["50001","50002","50003","50005","50006","50007","50008","50016","50020","50024","50038","50039","51100"]
```


### <a name="using-the--match-operator"></a>Usando o operador-match 
O operador **-Match** é usado para corresponder a qualquer expressão regular. Exemplos:

```
user.displayName -match "Da.*"   
```
Da, DAV, David avaliada como true, o aDa é avaliado como false.

```
user.displayName -match ".*vid"
```
David é avaliado como true, o da é avaliado como false.

## <a name="supported-values"></a>Valores suportados

Os valores usados em uma expressão podem consistir em vários tipos, incluindo:

* Cadeias
* Booliano – verdadeiro, falso
* Numbers
* Matrizes – matriz de números, matriz de cadeia de caracteres

Ao especificar um valor dentro de uma expressão, é importante usar a sintaxe correta para evitar erros. Algumas dicas de sintaxe são:

* Aspas duplas são opcionais, a menos que o valor seja uma cadeia de caracteres.
* Operações de cadeia de caracteres e Regex não diferenciam maiúsculas de minúsculas.
* Quando um valor de cadeia de caracteres contém aspas duplas, ambas as aspas devem ser precedidas usando o caractere \`, por exemplo, User. Department-EQ \`"Sales\`" é a sintaxe adequada quando "Sales" é o valor.
* Você também pode executar verificações nulas, usando NULL como um valor, por exemplo, `user.department -eq null`.

### <a name="use-of-null-values"></a>Uso de valores nulos

Para especificar um valor nulo em uma regra, você pode usar o valor *nulo* . 

* Use-EQ ou-ne ao comparar o valor *nulo* em uma expressão.
* Use aspas ao contrário da palavra *NULL* somente se desejar que ela seja interpretada como um valor de cadeia de caracteres literal.
* O operador-NOT não pode ser usado como um operador comparativa para NULL. Se você usá-lo, receberá um erro se usar nulo ou $null.

A maneira correta de fazer referência ao valor nulo é a seguinte:

```
   user.mail –ne null
```

## <a name="rules-with-multiple-expressions"></a>Regras com várias expressões

Uma regra de associação de grupo pode consistir em mais de uma única expressão conectada pelos operadores lógicos-and,-or e-not. Os operadores lógicos também podem ser usados em combinação. 

Veja a seguir exemplos de regras de associação construídas corretamente com várias expressões:

```
(user.department -eq "Sales") -or (user.department -eq "Marketing")
(user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")
```

### <a name="operator-precedence"></a>Precedência de operadores

Todos os operadores são listados abaixo em ordem de precedência do mais alto para o mais baixo. Os operadores na mesma linha são de precedência igual:

```
-eq -ne -startsWith -notStartsWith -contains -notContains -match –notMatch -in -notIn
-not
-and
-or
-any -all
```

Veja a seguir um exemplo de precedência de operador em que duas expressões estão sendo avaliadas para o usuário:

```
   user.department –eq "Marketing" –and user.country –eq "US"
```

Os parênteses são necessários apenas quando a precedência não atende às suas necessidades. Por exemplo, se você quiser que o departamento seja avaliado primeiro, o exemplo a seguir mostra como os parênteses podem ser usados para determinar a ordem:

```
   user.country –eq "US" –and (user.department –eq "Marketing" –or user.department –eq "Sales")
```

## <a name="rules-with-complex-expressions"></a>Regras com expressões complexas

Uma regra de associação pode consistir em expressões complexas nas quais as propriedades, os operadores e os valores assumem formas mais complexas. As expressões são consideradas complexas quando qualquer uma das seguintes opções são verdadeiras:

* A propriedade consiste em uma coleção de valores; especificamente, as propriedades com valores múltiplos
* As expressões usam os operadores-any e-All
* O valor da expressão pode ser uma ou mais expressões

## <a name="multi-value-properties"></a>Propriedades de vários valores

As propriedades de vários valores são coleções de objetos do mesmo tipo. Eles podem ser usados para criar regras de associação usando os operadores lógicos-any e-ALL.

| Propriedades | Valores | Utilização |
| --- | --- | --- |
| assignedPlans | Cada objeto na coleção expõe as seguintes propriedades de cadeia de caracteres: capabilityStatus, Service, serviço de enplaneid |User. assignedPlans-Any (assignedPlan. onplanid-EQ "efb87545-963c-4e0d-99df-69c6916d9eb0"-e assignedPlan. capabilityStatus-EQ "Enabled") |
| proxyAddresses| SMTP: alias@domain SMTP: alias@domain | (User. proxyAddresses-Any (\_-contém "contoso")) |

### <a name="using-the--any-and--all-operators"></a>Usando os operadores-any e-All

Você pode usar os operadores-any e-All para aplicar uma condição a um ou todos os itens na coleção, respectivamente.

* -qualquer (satisfeito quando pelo menos um item da coleção corresponde à condição)
* -todos (satisfeitos quando todos os itens na coleção correspondem à condição)

#### <a name="example-1"></a>Exemplo 1

assignedPlans é uma propriedade com vários valores que lista todos os planos de serviço atribuídos ao usuário. A expressão a seguir seleciona os usuários que têm o plano de serviço do Exchange Online (plano 2) (como um valor de GUID) que também está no estado habilitado:

```
user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled")
```

Uma regra como esta pode ser usada para agrupar todos os usuários para os quais um recurso do Office 365 (ou outro serviço online da Microsoft) esteja habilitado. Em seguida, você pode aplicar com um conjunto de políticas ao grupo.

#### <a name="example-2"></a>Exemplo 2

A expressão a seguir seleciona todos os usuários que têm qualquer plano de serviço associado ao serviço do Intune (identificado pelo nome do serviço "SCO"):

```
user.assignedPlans -any (assignedPlan.service -eq "SCO" -and assignedPlan.capabilityStatus -eq "Enabled")
```

### <a name="using-the-underscore-_-syntax"></a>Usando a sintaxe de sublinhado (\_)

A sintaxe de sublinhado (\_) corresponde a ocorrências de um valor específico em uma das propriedades de coleção de cadeia de caracteres com valores iguais para adicionar usuários ou dispositivos a um grupo dinâmico. Ele é usado com os operadores-any ou-ALL.

Aqui está um exemplo de como usar o sublinhado (\_) em uma regra para adicionar membros com base em User. proxyAddress (ele funciona da mesma para User. otherMails). Essa regra adiciona qualquer usuário com endereço de proxy que contenha "contoso" ao grupo.

```
(user.proxyAddresses -any (_ -contains "contoso"))
```

## <a name="other-properties-and-common-rules"></a>Outras propriedades e regras comuns

### <a name="create-a-direct-reports-rule"></a>Criar uma regra de "subordinados diretos"

Você pode criar um grupo que contém todos os subordinados diretos de um gerente. Quando os relatórios diretos do gerente forem alterados no futuro, a associação do grupo será ajustada automaticamente.

A regra de relatórios diretos é construída usando a seguinte sintaxe:

```
Direct Reports for "{objectID_of_manager}"
```

Aqui está um exemplo de uma regra válida em que "62e19b97-8b3d-4d4a-A106-4ce66896a863" é o objectID do gerente:

```
Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863"
```

As dicas a seguir podem ajudá-lo a usar a regra corretamente.

- A **ID do Gerenciador** é a ID de objeto do gerente. Ele pode ser encontrado no **perfil**do gerente.
- Para que a regra funcione, verifique se a propriedade **Manager** está definida corretamente para os usuários em seu locatário. Você pode verificar o valor atual no **perfil**do usuário.
- Esta regra dá suporte apenas aos subordinados diretos do gerente. Em outras palavras, você não pode criar um grupo com os relatórios diretos do gerente *e* seus relatórios.
- Esta regra não pode ser combinada com nenhuma outra regra de associação.

### <a name="create-an-all-users-rule"></a>Criar uma regra de "todos os usuários"

Você pode criar um grupo que contém todos os usuários em um locatário usando uma regra de associação. Quando os usuários são adicionados ou removidos do locatário no futuro, a associação do grupo é ajustada automaticamente.

A regra "todos os usuários" é construída usando uma única expressão usando o operador-ne e o valor nulo. Essa regra adiciona usuários de convidado B2B, bem como usuários Membros ao grupo.

```
user.objectid -ne null
```

### <a name="create-an-all-devices-rule"></a>Criar uma regra de "todos os dispositivos"

Você pode criar um grupo que contém todos os dispositivos dentro de um locatário usando uma regra de associação. Quando os dispositivos são adicionados ou removidos do locatário no futuro, a associação do grupo é ajustada automaticamente.

A regra "todos os dispositivos" é construída usando uma única expressão usando o operador-ne e o valor nulo:

```
device.objectid -ne null
```

## <a name="extension-properties-and-custom-extension-properties"></a>Propriedades de extensão e propriedades de extensão personalizadas

Atributos de extensão e propriedades de extensão personalizadas têm suporte como propriedades de cadeia de caracteres em regras de associação dinâmica. Os atributos de extensão são sincronizados do AD do servidor de janelas local e têm o formato "ExtensionAttributeX", onde X é igual a 1-15. Aqui está um exemplo de uma regra que usa um atributo de extensão como uma propriedade:

```
(user.extensionAttribute15 -eq "Marketing")
```

As propriedades de extensão personalizadas são sincronizadas do AD do Windows Server local ou de um aplicativo SaaS conectado e têm o formato de `user.extension_[GUID]_[Attribute]`, em que:

* [GUID] é o identificador exclusivo no Azure AD para o aplicativo que criou a propriedade no Azure AD
* [Attribute] é o nome da propriedade conforme ela foi criada

Um exemplo de uma regra que usa uma propriedade de extensão personalizada é:

```
user.extension_c272a57b722d4eb29bfe327874ae79cb_OfficeNumber -eq "123"
```

O nome da propriedade personalizada pode ser encontrado no diretório consultando a propriedade de um usuário usando o explorador do Graph e procurando o nome da propriedade. Além disso, agora você pode selecionar a link **obter propriedades de extensão personalizadas** no construtor de regras do grupo de usuários dinâmico para inserir uma ID de aplicativo exclusiva e receber a lista completa de propriedades de extensão personalizadas a serem usadas ao criar uma regra de associação dinâmica. Esta lista também pode ser atualizada para obter quaisquer novas propriedades de extensão personalizado para essa aplicação.

## <a name="rules-for-devices"></a>Regras para dispositivos

Você também pode criar uma regra que Selecione objetos de dispositivo para associação em um grupo. Você não pode ter usuários e dispositivos como membros do grupo. 

> [!NOTE]
> O atributo **OrganizationalUnit** não está mais listado e não deve ser usado. Essa cadeia de caracteres é definida pelo Intune em casos específicos, mas não é reconhecida pelo Azure AD, portanto, nenhum dispositivo é adicionado aos grupos com base nesse atributo.

> [!NOTE]
> systemlabels é um atributo somente leitura que não pode ser definido com o Intune.
>
> Para o Windows 10, o formato correto do atributo deviceOSVersion é o seguinte: (Device. deviceOSVersion-EQ "10,0 (17763)"). A formatação pode ser validada com o cmdlet Get-MsolDevice do PowerShell.

Os atributos de dispositivo a seguir podem ser usados.

 Atributo de dispositivo  | Valores | Exemplo
 ----- | ----- | ----------------
 accountEnabled | verdadeiro falso | (Device. accountEnabled-EQ true)
 displayName | qualquer valor de cadeia de caracteres |(device.displayName -eq "Rob iPhone")
 deviceOSType | qualquer valor de cadeia de caracteres | (device.deviceOSType -eq "iPad") -or (device.deviceOSType -eq "iPhone")<br>(Device. deviceOSType-contém "AndroidEnterprise")<br>(device.deviceOSType -eq "AndroidForWork")
 versãoDeSODoDispositivo | qualquer valor de cadeia de caracteres | (device.deviceOSVersion -eq "9.1")
 deviceCategory | um nome de categoria de dispositivo válido | (device.deviceCategory -eq "BYOD")
 deviceManufacturer | qualquer valor de cadeia de caracteres | (device.deviceManufacturer -eq "Samsung")
 deviceModel | qualquer valor de cadeia de caracteres | (device.deviceModel -eq "iPad Air")
 deviceOwnership | Pessoal, empresa, desconhecido | (Device. deviceOwnership-EQ "empresa")
 enrollmentProfileName | Perfil de registro de dispositivo da Apple, registro de dispositivo-identificadores de dispositivo corporativo (Android-quiosque) ou nome do perfil do Windows AutoPilot | (device.enrollmentProfileName -eq "DEP iPhones")
 isRooted | verdadeiro falso | (Device. isenraizadad-EQ true)
 managementType | MDM (para dispositivos móveis)<br>PC (para computadores gerenciados pelo agente de PC do Intune) | (device.managementType -eq "MDM")
 deviceId | uma ID de dispositivo do Azure AD válida | (device.deviceId -eq "d4fe7726-5966-431c-b3b8-cddc8fdb717d")
 objectId | uma ID de objeto do Azure AD válida |  (Device. objectId-EQ "76ad43c9-32c5-45e8-A272-7b58b58f596d")
 devicePhysicalIds | qualquer valor de cadeia de caracteres usado pelo AutoPilot, como todos os dispositivos AutoPilot, OrderID ou PurchaseOrderID  | (Device. devicePhysicalIDs-any _ contém "[ZTDId]") (Device. devicePhysicalIds-qualquer _-EQ "[OrderID]: 179887111881") (Device. devicePhysicalIds-qualquer _-EQ "[PurchaseOrderId]: 76222342342")
 systemLabels | qualquer cadeia de caracteres correspondente à propriedade de dispositivo do Intune para marcar dispositivos de local de trabalho modernos | (Device. systemLabels-contém "M365Managed")

> [!Note]  
> Para o deviceOwnership ao criar grupos dinâmicos para dispositivos, você precisa definir o valor igual a "Company". No Intune, a propriedade do dispositivo é representada como corporativo. Consulte [ownertypes](https://docs.microsoft.com/intune/reports-ref-devices#ownertypes) para obter mais detalhes. 

## <a name="next-steps"></a>Passos seguintes

Esses artigos fornecem informações adicionais sobre grupos no Azure Active Directory.

- [Ver grupos existentes](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Criar um novo grupo e adicionar membros](../fundamentals/active-directory-groups-create-azure-portal.md)
- [Gerir definições de um grupo](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Gerir associações de um grupo](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Gerir regras dinâmicas dos utilizadores num grupo](groups-create-rule.md)
