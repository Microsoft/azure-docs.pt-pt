---
title: Regras de associação de grupo dinâmico de automáticas - Azure Active Directory | Documentos da Microsoft
description: Como criar regras de associação para preencher automaticamente os grupos e uma referência de regra.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8a21e300c2540ddefb79fcaf593f752eef7e2c2e
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "57010196"
---
# <a name="dynamic-membership-rules-for-groups-in-azure-active-directory"></a>Regras de associação dinâmica para grupos no Azure Active Directory

No Azure Active Directory (Azure AD), pode criar regras baseadas em atributos complexas para ativar a filiação dinâmica para grupos. Associação de grupo dinâmica reduz a sobrecarga administrativa de adição e remoção de utilizadores. Este artigo fornece detalhes sobre as propriedades e a sintaxe para criar regras de associação dinâmica para utilizadores ou dispositivos. Pode configurar uma regra de filiação dinâmica em grupos de segurança ou grupos do Office 365.

Quando alterar a todos os atributos de um utilizador ou dispositivo, o sistema avalia todas as regras de grupos dinâmicos num diretório para ver se a alteração dispararia qualquer grupo adiciona ou remove. Se um utilizador ou dispositivo cumprir uma regra num grupo, eles são adicionados como um membro desse grupo. Se deixar de cumprir a regra, serão removidos. Manualmente, é possível adicionar ou remover um membro de um grupo dinâmico.

* Pode criar um grupo dinâmico para os dispositivos ou para os utilizadores, mas não é possível criar uma regra que contém utilizadores e dispositivos.
* Não é possível criar um grupo de dispositivos com base nos atributos dos proprietários de dispositivos. Regras de associação de dispositivo só podem referenciar os atributos do dispositivo.

> [!NOTE]
> Esta funcionalidade necessita de uma licença do Azure AD Premium P1 para cada utilizador exclusivo que é um membro de um ou mais grupos dinâmicos. Não é necessário atribuir licenças aos utilizadores para os mesmos para serem membros de grupos dinâmicos, mas tem de ter o número mínimo de licenças no inquilino para cobrir todos esses usuários. Por exemplo, se tivesse um total de 1000 utilizadores exclusivos em todos os grupos dinâmicos no seu inquilino, seria necessário pelo menos de 1000 licenças para o Azure AD Premium P1 cumprir o requisito de licença.
>

## <a name="constructing-the-body-of-a-membership-rule"></a>Construir o corpo de uma regra de associação

Uma regra de associação que povoa automaticamente a um grupo com utilizadores ou dispositivos é uma expressão binária, que resulta num resultado true ou false. As três partes de uma regra simple são:

* Propriedade
* Operador
* Value

A ordem das partes dentro de uma expressão são importantes para evitar erros de sintaxe.

### <a name="rules-with-a-single-expression"></a>Regras com uma única expressão

Uma única expressão é a forma mais simples de uma regra de associação e tem apenas três partes mencionadas acima. Uma regra com uma única expressão é semelhante a este: `Property Operator Value`, em que a sintaxe para a propriedade é o nome do object.property.

Segue-se um exemplo de uma regra de associação construído corretamente com uma única expressão:

```
user.department -eq "Sales"
```

Parênteses são opcionais para uma única expressão. O comprimento total do corpo de regra de associação não pode exceder 2048 carateres.

## <a name="supported-properties"></a>Propriedades suportadas

Existem três tipos de propriedades que podem ser usados para criar uma regra de associação.

* Booleano
* String
* Coleção de cadeia de caracteres

Seguem-se as propriedades de utilizador que pode utilizar para criar uma única expressão.

### <a name="properties-of-type-boolean"></a>Propriedades de tipo Booleano

| Propriedades | Valores permitidos | Utilização |
| --- | --- | --- |
| accountEnabled |Verdadeiro Falso |user.accountEnabled - eq true |
| dirSyncEnabled |Verdadeiro Falso |user.dirSyncEnabled -eq true |

### <a name="properties-of-type-string"></a>Propriedades do tipo string

| Propriedades | Valores permitidos | Utilização |
| --- | --- | --- |
| city |Qualquer valor de cadeia ou *nulo* |(user.city - eq "value") |
| País |Qualquer valor de cadeia ou *nulo* |(User. Country - eq "value") |
| companyName | Qualquer valor de cadeia ou *nulo* | (user.companyName -eq "value") |
| Departamento |Qualquer valor de cadeia ou *nulo* |(user.department -eq "value") |
| displayName |qualquer valor de cadeia |(user.displayName -eq "value") |
| employeeId |qualquer valor de cadeia |(user.employeeId -eq "value")<br>(user.employeeId - ne *nulo*) |
| facsimileTelephoneNumber |Qualquer valor de cadeia ou *nulo* |(user.facsimileTelephoneNumber -eq "value") |
| givenName |Qualquer valor de cadeia ou *nulo* |(user.givenName -eq "value") |
| jobTitle |Qualquer valor de cadeia ou *nulo* |(user.jobTitle -eq "value") |
| mail |Qualquer valor de cadeia ou *nulo* (endereço SMTP do utilizador) |(user.mail -eq "value") |
| mailNickName |Qualquer valor de cadeia de caracteres (alias de correio do utilizador) |(user.mailNickName -eq "value") |
| móvel |Qualquer valor de cadeia ou *nulo* |(user.mobile -eq "value") |
| objectId |GUID do objeto de utilizador |(user.objectId -eq "11111111-1111-1111-1111-111111111111") |
| onPremisesSecurityIdentifier | No local o identificador de segurança (SID) para os utilizadores que foram sincronizados no local para a cloud. |(user.onPremisesSecurityIdentifier -eq "S-1-1-11-1111111111-1111111111-1111111111-1111111") |
| passwordPolicies |None DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration, DisableStrongPassword |(user.passwordPolicies -eq "DisableStrongPassword") |
| physicalDeliveryOfficeName |Qualquer valor de cadeia ou *nulo* |(user.physicalDeliveryOfficeName -eq "value") |
| postalCode |Qualquer valor de cadeia ou *nulo* |(user.postalCode -eq "value") |
| preferredLanguage |Código do ISO 639-1 |(user.preferredLanguage -eq "en-US") |
| sipProxyAddress |Qualquer valor de cadeia ou *nulo* |(user.sipProxyAddress -eq "value") |
| state |Qualquer valor de cadeia ou *nulo* |(user.state -eq "value") |
| streetAddress |Qualquer valor de cadeia ou *nulo* |(user.streetAddress -eq "value") |
| Apelido |Qualquer valor de cadeia ou *nulo* |(user.surname -eq "value") |
| telephoneNumber |Qualquer valor de cadeia ou *nulo* |(user.telephoneNumber -eq "value") |
| usageLocation |Indicativo de país de letras dois |(user.usageLocation -eq "US") |
| userPrincipalName |qualquer valor de cadeia |(user.userPrincipalName -eq "alias@domain") |
| userType |o convidado de membro *nulo* |(user.userType -eq "Member") |

### <a name="properties-of-type-string-collection"></a>Propriedades de coleção de cadeia de caracteres de tipo

| Propriedades | Valores permitidos | Utilização |
| --- | --- | --- |
| otherMails |qualquer valor de cadeia |(user.otherMails -contains "alias@domain") |
| proxyAddresses |SMTP: alias@domain smtp: alias@domain |(user.proxyAddresses-contém "SMTP: alias@domain") |

Para as propriedades utilizadas para regras de dispositivo, consulte [regras para dispositivos](#rules-for-devices).

## <a name="supported-operators"></a>Operadores suportados

A tabela seguinte apresenta uma lista de todos os operadores suportados e sua sintaxe para uma única expressão. Operadores podem ser utilizados com ou sem o prefixo de hífen (-).

| Operador | Sintaxe |
| --- | --- |
| Não é igual a |-ne |
| Igual a |-eq |
| Não começa com |-notStartsWith |
| Começa com |-startsWith |
| Não contém |-notContains |
| Contains |-contains |
| Não corresponde |-notMatch |
| Correspondência |-match |
| Em | -in |
| Não em | -notIn |

### <a name="using-the--in-and--notin-operators"></a>Usando a opção - no e - notIn operadores

Se deseja comparar o valor de um atributo de utilizador em relação a um número de valores diferentes pode utilizar a opção - na ou - notIn operadores. Utilize os símbolos de colchete "[" e "]" para começar e terminar a lista de valores.

 No exemplo seguinte, a expressão avalia como VERDADEIRO se o valor de user.department for igual a qualquer um dos valores na lista:

```
   user.department -in ["50001","50002","50003","50005","50006","50007","50008","50016","50020","50024","50038","50039","51100"]
```


### <a name="using-the--match-operator"></a>Com o operador – match 
O **-corresponder** operador é utilizado para corresponder a qualquer expressão regular. Exemplos:

```
user.displayName -match "Da.*"   
```
David da, Dav, avaliadas como verdadeiras, aDa for avaliada como falsa.

```
user.displayName -match ".*vid"
```
David é avaliada como true, – pacote de iniciação for avaliada como falsa.

## <a name="supported-values"></a>Valores suportados

Os valores utilizados numa expressão podem ser composta por vários tipos, incluindo:

* Cadeias
* Booleano – verdadeiro, FALSO
* Números
* Matrizes-matriz de número, matriz de cadeia de caracteres

Quando especificar um valor dentro de uma expressão é importante usar a sintaxe correta para evitar erros. Algumas dicas de sintaxe são:

* Aspas duplas são opcionais, a menos que o valor é uma cadeia de caracteres.
* As operações de cadeia de caracteres e regex não estão maiúsculas de minúsculas.
* Quando um valor de cadeia de caracteres contém entre aspas duplas, ambas as aspas devem ter escritas utilizando o \` caráter, por exemplo, user.department - eq \`"vendas\`" é a sintaxe correta quando "Vendas" é o valor.
* Também pode executar verificações de Null, null a utilizar como um valor, por exemplo, `user.department -eq null`.

### <a name="use-of-null-values"></a>Utilização de valores nulos

Para especificar um valor nulo numa regra, pode utilizar o *nulo* valor. 

* Utilize - eq ou - ne, ao comparar os *nulo* valor numa expressão.
* Use aspas ao redor do mundo *nulo* apenas se deseja que ele deve ser interpretado como um valor de cadeia literal.
* -Não operador não pode ser utilizado como um operador comparativa para nulo. Se usá-la, obterá um erro se usar nulo ou $null.

A maneira correta para o valor nulo de referência é o seguinte:

```
   user.mail –ne null
```

## <a name="rules-with-multiple-expressions"></a>Regras com várias expressões

Uma regra de associação de grupo pode consistir em mais do que uma única expressão ligada por- e, - ou e - operadores lógicos não. Operadores lógicos também podem ser utilizados em combinação. 

Seguem-se exemplos de regras de associação construído corretamente com várias expressões:

```
(user.department -eq "Sales") -or (user.department -eq "Marketing")
(user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")
```

### <a name="operator-precedence"></a>Precedência de operador

Todos os operadores estão listados abaixo na ordem de precedência de mais alto ao mais baixo. Operadores na mesma linha têm precedência igual:

```
-eq -ne -startsWith -notStartsWith -contains -notContains -match –notMatch -in -notIn
-not
-and
-or
-any -all
```

Segue-se um exemplo de precedência de operador em que as duas expressões estão a ser avaliadas para o utilizador:

```
   user.department –eq "Marketing" –and user.country –eq "US"
```

Parênteses são necessárias apenas quando a precedência não cumpre os requisitos. Por exemplo, se pretender que o departamento para ser avaliada em primeiro lugar, o código a seguir mostra como os parênteses podem ser usados para determinar a ordem:

```
   user.country –eq "US" –and (user.department –eq "Marketing" –or user.department –eq "Sales")
```

## <a name="rules-with-complex-expressions"></a>Regras com expressões complexas

Uma regra de associação pode consistir em expressões complexas em que as propriedades, operadores e valores demorar em formulários mais complexos. As expressões são consideradas complexas quando se verificar qualquer um dos seguintes procedimentos:

* A propriedade consiste numa coleção de valores; especificamente, as propriedades com múltiplos valores
* As expressões de utilizam a opção - qualquer - todos os operadores e
* Se o valor da expressão pode ser uma ou mais expressões

## <a name="multi-value-properties"></a>Propriedades de valores múltiplos

Com múltiplos valores propriedades são coleções de objetos do mesmo tipo. Eles podem ser usados para criar regras de associação usando-os e - todos os operadores lógicos.

| Propriedades | Valores | Utilização |
| --- | --- | --- |
| assignedPlans | Cada objeto da coleção expõe as seguintes propriedades de cadeia de caracteres: capabilityStatus, serviço, servicePlanId |user.assignedPlans-qualquer (assignedPlan.servicePlanId - eq "efb87545-963c-4e0d-99df-69c6916d9eb0"- e assignedPlan.capabilityStatus - eq "Ativada") |
| proxyAddresses| SMTP: alias@domain smtp: alias@domain | (user.proxyAddresses-qualquer (\_ -contém "contoso")) |

### <a name="using-the--any-and--all-operators"></a>Usando a opção - qualquer - todos os operadores e

Pode utilizar - qualquer - todos os operadores e para aplicar uma condição a um ou todos os itens da coleção, respectivamente.

* -qualquer (satisfeitos quando, pelo menos, um item na coleção corresponde a condição)
* -todos (satisfeitos quando a condição correspondem a todos os itens na coleção)

#### <a name="example-1"></a>Exemplo 1

o assignedPlans é uma propriedade de valor múltiplos que apresenta uma lista de todos os planos de serviço atribuídos ao utilizador. A seguinte expressão seleciona os utilizadores que têm o plano do serviço Exchange Online (plano 2) (como um valor GUID) que também está no Estado ativado:

```
user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled")
```

Uma regra como este pode ser utilizada para agrupar todos os utilizadores aos quais um Office 365 (ou outro serviço Online da Microsoft) capacidade for disponibilizada. Em seguida, pode aplicar com um conjunto de políticas para o grupo.

#### <a name="example-2"></a>Exemplo 2

A seguinte expressão seleciona todos os utilizadores com qualquer plano de serviço que está associado com o serviço do Intune (identificado pelo nome de serviço "SCO"):

```
user.assignedPlans -any (assignedPlan.service -eq "SCO" -and assignedPlan.capabilityStatus -eq "Enabled")
```

### <a name="using-the-underscore--syntax"></a>Utilizar o caráter de sublinhado (\_) sintaxe

O caráter de sublinhado (\_) corresponde a sintaxe de ocorrências de um valor específico em uma das propriedades de coleção com múltiplos valores de cadeia de caracteres para adicionar utilizadores ou dispositivos a um grupo dinâmico. É utilizado com a opção - qualquer ou - todos os operadores.

Eis um exemplo de como utilizar o caráter de sublinhado (\_) numa regra para adicionar membros com base nos user.proxyAddress (ele funciona da mesma para user.otherMails). Esta regra adiciona qualquer utilizador com o endereço de proxy que contém "contoso" ao grupo.

```
(user.proxyAddresses -any (_ -contains "contoso"))
```

## <a name="other-properties-and-common-rules"></a>Outras propriedades e as regras comuns

### <a name="create-a-direct-reports-rule"></a>Criar uma regra de "Direct reports"

Pode criar um grupo que contém todos os relatórios diretos de um gerente. Quando direto os o Gestor relatórios são alterados no futuro, a associação do grupo é ajustada automaticamente.

A regra de relatórios diretos é construído de acordo com a seguinte sintaxe:

```
Direct Reports for "{objectID_of_manager}"
```

Eis um exemplo de uma regra de válida em que "62e19b97-8b3d-4d4a-a106-4ce66896a863" é o objectID do Gerenciador de:

```
Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863"
```

As sugestões seguintes podem ajudá-lo a utilizar a regra corretamente.

* O **ID de gestor** é o ID de objeto do gestor. Podem ser encontrados no Gestor de **perfil**.
* Para a regra funcionar, certifique-se de que o **Manager** propriedade está definida corretamente para os utilizadores no seu inquilino. Pode verificar o valor atual do usuário **perfil**.
* Esta regra suporta apenas os relatórios do Gestor direto. Em outras palavras, não é possível criar um grupo com relatórios diretos do Gestor *e* seus relatórios.
* Esta regra não pode ser combinada com outras regras de associação.

### <a name="create-an-all-users-rule"></a>Criar uma regra de "Todos os utilizadores"

Pode criar um grupo que contém todos os utilizadores num inquilino utilizando uma regra de associação. Quando os utilizadores são adicionados ou removidos do inquilino no futuro, a associação do grupo é ajustada automaticamente.

A regra de "Todos os utilizadores" é construído de acordo com a expressão única que utiliza o operador - ne e o valor nulo. Esta regra adiciona utilizadores convidados B2B, bem como os utilizadores membros ao grupo.

```
user.objectid -ne null
```

### <a name="create-an-all-devices-rule"></a>Criar uma regra de "Todos os dispositivos"

Pode criar um grupo que contém todos os dispositivos dentro de um inquilino através de uma regra de associação. Quando os dispositivos são adicionados ou removidos do inquilino no futuro, a associação do grupo é ajustada automaticamente.

A regra de "Todos os dispositivos" é construído de acordo com a expressão única que utiliza o operador - ne e o valor nulo:

```
device.objectid -ne null
```

## <a name="extension-properties-and-custom-extension-properties"></a>Propriedades de extensão e as propriedades de extensão personalizado

Atributos de extensão e propriedades de extenson personalizadas são suportadas como propriedades de cadeia de caracteres em regras de associação dinâmica. Atributos de extensão são sincronizados a partir de locais de Windows Server AD e tirar o formato de "ExtensionAttributeX", onde X é igual a 1 a 15. Eis um exemplo de uma regra que utiliza um atributo de extensão como uma propriedade de:

```
(user.extensionAttribute15 -eq "Marketing")
```

Propriedades de extensão personalizados são sincronizadas a partir do Windows Server do AD no local ou a partir de um aplicativo SaaS conectado e estão no formato de `user.extension_[GUID]__[Attribute]`, em que:

* [GUID] é o identificador exclusivo no Azure AD para a aplicação que criou a propriedade no Azure AD
* [Atributo] é o nome da propriedade como ele foi criado

Um exemplo de uma regra que utiliza uma propriedade de extensão personalizado é:

```
user.extension_c272a57b722d4eb29bfe327874ae79cb__OfficeNumber -eq "123"
```

O nome de propriedade personalizada pode ser encontrado no diretório ao consultar um utilizador da propriedade com o Explorador do gráfico e procurar o nome da propriedade. Além disso, pode agora selecionar **obter propriedades de extensão personalizado** link no construtor de regra de grupo dinâmico de utilizador para introduzir um ID de aplicação exclusivo e receber a lista completa de propriedades de extensão personalizada a utilizar ao criar uma regra de associação de grupo dinâmica. Esta lista também pode ser atualizada para obter quaisquer novas propriedades de extensão personalizado para essa aplicação.

## <a name="rules-for-devices"></a>Regras para dispositivos

Também pode criar uma regra de que seleciona objetos de dispositivo para a associação num grupo. Não pode ter dispositivos e utilizadores como membros do grupo. O **organizationalUnit** atributo já não está listado e não deve ser utilizado. Esta cadeia é definida pelo Intune em casos específicos, mas não é reconhecida pelo Azure AD, para que os dispositivos não são adicionados a grupos com base nesse atributo.

Os seguintes atributos do dispositivo podem ser utilizados.

 Atributo do dispositivo  | Valores | Exemplo
 ----- | ----- | ----------------
 accountEnabled | Verdadeiro Falso | (device.accountEnabled - eq verdadeiro)
 displayName | qualquer valor de cadeia |(device.displayName -eq "Rob Iphone”)
 deviceOSType | qualquer valor de cadeia | (device.deviceOSType -eq "iPad") -or (device.deviceOSType -eq "iPhone")
 deviceOSVersion | qualquer valor de cadeia | (device.deviceOSVersion -eq "9.1")
 deviceCategory | um nome de categoria de dispositivo válido | (device.deviceCategory -eq "BYOD")
 deviceManufacturer | qualquer valor de cadeia | (device.deviceManufacturer -eq "Samsung")
 deviceModel | qualquer valor de cadeia | (device.deviceModel -eq "iPad Air")
 deviceOwnership | Pessoal, empresa, desconhecido | (device.deviceOwnership - eq "Empresa")
 domainName | qualquer valor de cadeia | (device.domainName -eq "contoso.com")
 enrollmentProfileName | Nome do perfil de perfil de inscrição de dispositivos da Apple ou do Windows Autopilot | (device.enrollmentProfileName -eq "DEP iPhones")
 isRooted | Verdadeiro Falso | (device.isRooted - eq verdadeiro)
 managementType | MDM (para dispositivos móveis)<br>PC (para computadores geridos pelo agente de PC do Intune) | (device.managementType -eq "MDM")
 deviceId | um ID de dispositivo do Azure AD | (device.deviceId -eq "d4fe7726-5966-431c-b3b8-cddc8fdb717d")
 objectId | ID de objeto de um válido do Azure AD |  (device.objectId -eq 76ad43c9-32c5-45e8-a272-7b58b58f596d")
 systemLabels | qualquer cadeia de caracteres que a propriedade de dispositivo do Intune para dispositivos modernos à área de trabalho de marcação | (device.systemLabels -contains "M365Managed")

> [!Note]  
> Para o deviceOwnership quando criar grupos dinâmicos para dispositivos que precisa para definir o valor igual a "Empresa". Sobre o Intune em vez disso, a propriedade do dispositivo é representada como empresa. Consulte a [Tiposdeproprietário](https://docs.microsoft.com/intune/reports-ref-devices#ownertypes) para obter mais detalhes. 

## <a name="next-steps"></a>Passos Seguintes

Estes artigos fornecem informações adicionais sobre os grupos no Azure Active Directory.

* [Ver grupos existentes](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Criar um novo grupo e adicionar membros](../fundamentals/active-directory-groups-create-azure-portal.md)
* [Gerir definições de um grupo](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Gerir associações de um grupo](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Gerir regras dinâmicas dos utilizadores num grupo](groups-dynamic-membership.md)
