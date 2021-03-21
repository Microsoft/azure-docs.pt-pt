---
title: Sincronização de identidade e duplicação de atributos resiliência | Microsoft Docs
description: Novo comportamento de como lidar com objetos com conflitos UPN ou ProxyAddress durante a sincronização de diretório usando Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 537a92b7-7a84-4c89-88b0-9bce0eacd931
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/15/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e09dd6a127bd04ae698cb6cad2ffd7f35e3b51c3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94413433"
---
# <a name="identity-synchronization-and-duplicate-attribute-resiliency"></a>Sincronização de identidades e resiliência de atributos duplicados
Duplicado Attribute Resiliency é uma funcionalidade no Azure Ative Directory que eliminará o atrito causado pelos conflitos **do UserPrincipalName** e do SMTP **ProxyAddress** ao executar uma das ferramentas de sincronização da Microsoft.

Estes dois atributos são geralmente necessários para serem únicos em todos os objetos **do Utilizador,** **Grupo** ou **Contacto** num determinado inquilino do Azure Ative Directory.

> [!NOTE]
> Apenas os Utilizadores podem ter UPNs.
> 
> 

O novo comportamento que esta funcionalidade permite está na parte da nuvem do pipeline de sincronização, pelo que é agnóstico do cliente e relevante para qualquer produto de sincronização da Microsoft, incluindo Azure AD Connect, DirSync e MIM + Connector. O termo genérico "cliente sincronizado" é usado neste documento para representar qualquer um destes produtos.

## <a name="current-behavior"></a>Comportamento atual
Se houver uma tentativa de fornecimento de um novo objeto com um valor UPN ou ProxyAddress que viole esta restrição de singularidade, o Azure Ative Directory bloqueia a criação de um objeto. Da mesma forma, se um objeto for atualizado com uma UPN ou ProxyAddress não única, a atualização falha. A tentativa ou atualização do provisionamento é novamente julgada pelo cliente sincronizado em cada ciclo de exportação, e continua a falhar até que o conflito seja resolvido. Um e-mail de relatório de erro é gerado em cada tentativa e um erro é registado pelo cliente sincronizado.

## <a name="behavior-with-duplicate-attribute-resiliency"></a>Comportamento com duplicado atributo resiliência
Em vez de não prever ou atualizar completamente um objeto com um atributo duplicado, o Azure Ative Directory "coloca em quarentena" o atributo duplicado que violaria a restrição de singularidade. Se este atributo for necessário para o provisionamento, como o UserPrincipalName, o serviço atribui um valor reservado. O formato destes valores temporários é  
_**\<OriginalPrefix> + \<4DigitNumber> \@ \<InitialTenantDomain> .onmicrosoft.com.**_

O processo de resiliência do atributo lida apenas com os valores UPN e SMTP **ProxyAddress.**

Se o atributo não for necessário, como um  **ProxyAddress,** o Azure Ative Directory simplesmente coloca em quarentena o atributo de conflito e procede à criação ou atualização do objeto.

Após a quarentena do atributo, a informação sobre o conflito é enviada no mesmo e-mail de relatório de erro usado no comportamento antigo. No entanto, esta informação só aparece no relatório de erro uma vez, quando a quarentena acontece, não continua a ser registada em futuros e-mails. Além disso, uma vez que a exportação para este objeto foi bem sucedida, o cliente sincronizado não regista um erro e não volta a tentar a operação de criação/atualização nos ciclos de sincronização subsequentes.

Para suportar este comportamento foi adicionado um novo atributo às classes de objetos de Utilizador, Grupo e Contacto:  
**DirSyncProvisioningErrors**

Este é um atributo multi-valor que é usado para armazenar os atributos contraditórios que violariam a restrição de singularidade caso fossem adicionados normalmente. Foi ativada uma tarefa de temporizador de fundo no Azure Ative Directory, que corre de hora em hora para procurar conflitos de atributos duplicados que tenham sido resolvidos e remova automaticamente os atributos em questão da quarentena.

### <a name="enabling-duplicate-attribute-resiliency"></a>Habilitar a resiliência do atributo duplicado
Duplicado Atributo Resiliência será o novo comportamento padrão em todos os inquilinos do Azure Ative Directory. Será por defeito para todos os inquilinos que permitiram a sincronização pela primeira vez no dia 22 de agosto de 2016 ou mais tarde. Os inquilinos que ativaram a sincronização antes desta data terão a funcionalidade ativada em lotes. Este lançamento terá início em setembro de 2016, sendo enviada uma notificação por e-mail para o contacto técnico de notificação de cada inquilino com a data específica em que a funcionalidade será ativada.

> [!NOTE]
> Uma vez ligada a resiliência do atributo duplicado, não pode ser desativada.

Para verificar se a funcionalidade está ativada para o seu inquilino, pode fazê-lo descarregando a versão mais recente do módulo PowerShell do Diretório Ativo Azure e funcionando:

`Get-MsolDirSyncFeatures -Feature DuplicateUPNResiliency`

`Get-MsolDirSyncFeatures -Feature DuplicateProxyAddressResiliency`

> [!NOTE]
> Já não é possível utilizar Set-MsolDirSyncFeature cmdlet para ativar proativamente a função Duplicate Attribute Resiliency antes de ser ligada ao seu inquilino. Para poder testar a funcionalidade, terá de criar um novo inquilino do Azure Ative Directory.

## <a name="identifying-objects-with-dirsyncprovisioningerrors"></a>Identificação de Objetos com DirSyncProvisioningErrors
Existem atualmente dois métodos para identificar objetos que têm estes erros devido a conflitos de propriedade duplicados, a Azure Ative Directory PowerShell e o [centro de administração microsoft 365](https://admin.microsoft.com). No futuro, há planos para alargar a informação adicional baseada em portal.

### <a name="azure-active-directory-powershell"></a>Azure Active Directory do PowerShell
Para os cmdlets PowerShell neste tópico, o seguinte é verdade:

* Todos os cmdlets seguintes são sensíveis a maiíssculas.
* O **-ErrorCategory PropertyConflict** deve ser sempre incluído. Atualmente não existem outros tipos de **ErrorCategoria**, mas este pode ser alargado no futuro.

Primeiro, começa por executar **o Connect-MsolService** e introduzir credenciais para um administrador de inquilino.

Em seguida, utilize os seguintes cmdlets e operadores para ver os erros de diferentes maneiras:

1. [Ver Todos](#see-all)
2. [Por Tipo de Propriedade](#by-property-type)
3. [Por Valor Conflituoso](#by-conflicting-value)
4. [Usando uma pesquisa de cordas](#using-a-string-search)
5. Sorted
6. [Em uma quantidade limitada ou tudo](#in-a-limited-quantity-or-all)

#### <a name="see-all"></a>Ver todos
Uma vez ligado, para ver uma lista geral de erros de provisionamento de atributos no arrendatário executar:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict`

Isto produz um resultado como o seguinte:  
 ![Get-MsolDirSyncProvisioningError](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/1.png "Get-MsolDirSyncProvisioningError")  

#### <a name="by-property-type"></a>Por tipo de propriedade
Para ver erros por tipo de propriedade, adicione a bandeira **-PropertyName** com o argumento **UserPrincipalName** ou **ProxyAddresses:**

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName UserPrincipalName`

Ou

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName ProxyAddresses`

#### <a name="by-conflicting-value"></a>Por valor conflituoso
Para ver erros relacionados com uma propriedade específica adicione a bandeira **-PropertyValue** **(-PropertyName** deve ser usado também ao adicionar esta bandeira):

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyValue User@domain.com -PropertyName UserPrincipalName`

#### <a name="using-a-string-search"></a>Usando uma pesquisa de cordas
Para fazer uma pesquisa de cordas largas use a bandeira **-SearchString.** Isto pode ser usado independentemente de todas as bandeiras acima, com exceção de **-ErrorCategory PropertyConflict,** que é sempre necessário:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -SearchString User`

#### <a name="in-a-limited-quantity-or-all"></a>Em uma quantidade limitada ou todos
1. **MaxResults \<Int>** pode ser usado para limitar a consulta a um número específico de valores.
2. **Todos** podem ser usados para garantir que todos os resultados sejam recuperados no caso de existirem um grande número de erros.

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -MaxResults 5`

## <a name="microsoft-365-admin-center"></a>Microsoft 365 admin center (Centro de administração do Microsoft 365)
Pode ver erros de sincronização de diretórios no centro de administração microsoft 365. O relatório no centro de administração Microsoft 365 apenas exibe objetos **do Utilizador** que têm estes erros. Não mostra informações sobre conflitos entre **grupos** e **contactos.**

![Screenshot que mostra erros de sincronização de diretórios no centro de administração microsoft 365.](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/1234.png "Utilizadores Ativos")

Para obter instruções sobre como visualizar erros de sincronização de diretórios no centro de administração microsoft 365, consulte [identificar erros de sincronização de diretórios na Microsoft 365](https://support.office.com/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067).

### <a name="identity-synchronization-error-report"></a>Relatório de erro de sincronização de identidade
Quando um objeto com um conflito de atributos duplicado é tratado com este novo comportamento, uma notificação é incluída no e-mail padrão do Relatório de Erro de Sincronização de Identidade que é enviado para o contacto de Notificação Técnica para o inquilino. No entanto, há uma mudança importante neste comportamento. No passado, as informações sobre um conflito de atributos duplicados seriam incluídas em todos os relatórios de erro subsequentes até que o conflito fosse resolvido. Com este novo comportamento, a notificação de erro de um dado conflito só aparece uma vez no momento em que o atributo conflituoso é colocado em quarentena.

Aqui está um exemplo de como é a notificação de e-mail para um conflito ProxyAddress:  
    ![Screenshot que mostra um exemplo de uma notificação de e-mail para um conflito ProxyAddress.](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/6.png "Utilizadores Ativos")  

## <a name="resolving-conflicts"></a>Resolução de conflitos
As táticas de estratégia e resolução de resolução de problemas para estes erros não devem diferir da forma como os erros duplicados foram tratados no passado. A única diferença é que a tarefa do temporizador varre o inquilino do lado do serviço para adicionar automaticamente o atributo em questão ao objeto adequado uma vez que o conflito é resolvido.

O seguinte artigo descreve várias estratégias de resolução e resolução de problemas: [Atributos duplicados ou inválidos impedem a sincronização do diretório no Office 365](https://support.microsoft.com/kb/2647098).

## <a name="known-issues"></a>Problemas conhecidos
Nenhum destes problemas conhecidos causa perda de dados ou degradação do serviço. Vários deles são estéticos, outros fazem com que erros duplicados de atributos padrão "*pré-resiliência*" sejam lançados em vez de colocar em quarentena o atributo de conflito, e outro faz com que certos erros exijam uma correção manual extra.

**Comportamento central:**

1. Os objetos com configurações específicas de atributos continuam a receber erros de exportação em oposição ao ou os atributos duplicados que estão em quarentena.  
   Por exemplo:
   
    a. O novo utilizador é criado em AD com uma UPN de **Joe \@ contoso.com** e ProxyAddress **smtp:Joe \@ contoso.com**
   
    b. As propriedades deste objeto entram em conflito com um grupo existente, onde o ProxyAddress é **SMTP:Joe \@ contoso.com**.
   
    c. Após a exportação, um erro de **conflito proxyAddress** é lançado em vez de ter os atributos de conflito em quarentena. A operação é novamente experimentada em cada ciclo de sincronização subsequente, como teria sido antes da funcionalidade de resiliência ter sido ativada.
2. Se dois grupos forem criados no local com o mesmo endereço SMTP, um não prevê na primeira tentativa com um erro **proxyaddress duplicado** padrão. No entanto, o valor duplicado está devidamente colocado em quarentena no próximo ciclo de sincronização.

**Relatório do Portal do Escritório:**

1. A mensagem de erro detalhada para dois objetos num conjunto de conflitos upn é a mesma. Isto indica que ambos tiveram a sua UPN alterada/em quarentena, quando na verdade apenas um deles tinha quaisquer dados alterados.
2. A mensagem de erro detalhada para um conflito upn mostra o nome de exibição errado para um utilizador que teve a sua UPN alterada/quarentena. Por exemplo:
   
    a. **Utilizador A** sincroniza-se primeiro com **a UPN = Contoso.com do utilizador \@**.
   
    b. **O utilizador B** é tentado ser sincronizado em seguida com **UPN = Contoso.com do utilizador \@**.
   
    c. **Utilizador B's** A UPN é alterada para **\@ contoso.onmicrosoft.com do Utilizador1234** e **o \@ contoso.com do utilizador** é adicionado à **DirSyncProvisioningErrors**.
   
    d. A mensagem de erro **do Utilizador B** deve indicar que o Utilizador **A** já tem **\@ contoso.com do Utilizador** como UPN, mas mostra o próprio nome de visualização do Utilizador **B.**

**Relatório de erro de sincronização de identidade:**

O link para *etapas sobre como resolver esta questão* é incorreto:  
    ![Utilizadores Ativos](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/6.png "Utilizadores Ativos")  

Deve apontar [https://aka.ms/duplicateattributeresiliency]() para.

## <a name="see-also"></a>Ver também
* [Sincronização Azure Ad Connect](how-to-connect-sync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)
* [Identifique erros de sincronização de diretórios na Microsoft 365](https://support.office.com/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067)