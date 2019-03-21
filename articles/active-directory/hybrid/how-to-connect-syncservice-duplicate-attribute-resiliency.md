---
title: Resiliência de atributos de sincronização e duplicação de identidade | Documentos da Microsoft
description: Novo comportamento de como lidar com objetos com o UPN ou ProxyAddress conflitos durante a sincronização de diretórios através do Azure AD Connect.
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
ms.topic: conceptual
ms.date: 01/15/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc27e5cd6af19f06a5eab73e30d3034fada0ccc2
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57838396"
---
# <a name="identity-synchronization-and-duplicate-attribute-resiliency"></a>Sincronização de identidades e resiliência de atributos duplicados
Resiliência de atributos duplicados é uma funcionalidade do Azure Active Directory que eliminarão fricção causada por **UserPrincipalName** e **ProxyAddress** está em conflito ao executar um da Microsoft ferramentas de sincronização.

Esses dois atributos geralmente têm de ser exclusivo em todo **usuário**, **grupo**, ou **contacto** objetos num determinado inquilino do Azure Active Directory.

> [!NOTE]
> Apenas os utilizadores podem ter UPNs.
> 
> 

O novo comportamento que esta funcionalidade permite que está a parte da nuvem do pipeline de sincronização, portanto é agnóstico e relevante para qualquer produto de sincronização Microsoft, incluindo o Azure AD Connect, o DirSync e o MIM + o conector de cliente. O termo genérico "cliente de sincronização" é utilizado neste documento para representar qualquer um desses produtos.

## <a name="current-behavior"></a>Comportamento atual
Se houver uma tentativa para aprovisionar um novo objeto com um valor UPN ou ProxyAddress que viola esta restrição de exclusividade, o Azure Active Directory bloqueia esse objeto de que está sendo criado. Da mesma forma, se um objeto é atualizado com um UPN ou ProxyAddress exclusivos, a atualização falha. A tentativa de aprovisionamento ou a atualização será repetida pelo cliente de sincronização após cada ciclo de exportação e continua a falhar até que o conflito é resolvido. Uma mensagem de e-mail do relatório de erro é gerada após cada tentativa e erro é registado pelo cliente de sincronização.

## <a name="behavior-with-duplicate-attribute-resiliency"></a>Comportamento com resiliência de atributos duplicados
Em vez de completamente a falhar aprovisionar ou atualizar um objeto com um atributo duplicado, Azure Active Directory "coloca em quarentena" o atributo duplicado que violaria a restrição de exclusividade. Se este atributo é necessário para o aprovisionamento, como UserPrincipalName, o serviço atribui um valor de marcador de posição. O formato destes valores temporário é  
"***<OriginalPrefix>+ < 4DigitNumber >\@<InitialTenantDomain>. onmicrosoft.com***".  
Se o atributo não for necessário, como um **ProxyAddress**, Azure Active Directory simplesmente coloca em quarentena o atributo de conflito e prossegue com a criação do objeto ou a atualização.

Após o atributo de modo de quarentena, são enviadas informações sobre o conflito no mesmo e-mail de relatório de erro utilizado no comportamento antigo. No entanto, estas informações só é apresentado no relatório de erros uma vez, quando ocorre a quarentena, ele não continua a ser registadas nos e-mails futuras. Além disso, uma vez que a exportação para este objeto foi concluída com êxito, o cliente de sincronização não regista um erro e não repete a ação criar / atualizar a operação em ciclos de sincronização.

Para oferecer suporte a esse comportamento foi adicionado um novo atributo para as classes de objeto do utilizador, grupo e contacte:  
**DirSyncProvisioningErrors**

Este é um atributo com múltiplos valor, que é utilizado para armazenar os atributos em conflito que violam a restrição de exclusividade devem ser adicionados normalmente. Uma tarefa de temporizador de segundo plano foi ativada no Azure Active Directory é executada a cada hora para procurar por conflitos de atributos duplicados que já tenham sido resolvidos e remove automaticamente os atributos em questão da quarentena.

### <a name="enabling-duplicate-attribute-resiliency"></a>Ativar a resiliência de atributos duplicados
Resiliência de atributos duplicados será o novo comportamento de predefinição em todos os inquilinos do Azure Active Directory. Estará por predefinição para todos os inquilinos que permitia a sincronização pela primeira vez no dia 22 de Agosto de 2016 ou posterior. Os inquilinos que a sincronização até essa data ativada terão a funcionalidade ativada em lotes. Começar a esta implementação em Setembro de 2016 e uma notificação por e-mail será enviada para o contacto de notificação técnica de cada inquilino com o data específica quando a funcionalidade será ativada.

> [!NOTE]
> Depois de resiliência de atributos duplicados foi ativada não pode ser desativada.

Para verificar se a funcionalidade está ativada para o seu inquilino, pode fazê-ao transferir a versão mais recente do módulo Azure Active Directory PowerShell e em execução:

`Get-MsolDirSyncFeatures -Feature DuplicateUPNResiliency`

`Get-MsolDirSyncFeatures -Feature DuplicateProxyAddressResiliency`

> [!NOTE]
> Já não pode utilizar o cmdlet Set-MsolDirSyncFeature proativamente ativar a funcionalidade de resiliência de atributos duplicados antes de ele está ativado para o seu inquilino. Para poder-se de que a funcionalidade de teste, terá de criar um novo inquilino do Azure Active Directory.

## <a name="identifying-objects-with-dirsyncprovisioningerrors"></a>Identificando objetos com DirSyncProvisioningErrors
Existem atualmente dois métodos para identificar objetos que tenham estes erros devido a conflitos de propriedade duplicados, Azure Active Directory PowerShell e o Portal de administração do Office 365. Existem planos para expandir para o portal baseada em relatórios adicionais no futuro.

### <a name="azure-active-directory-powershell"></a>Azure Active Directory do PowerShell
Para os cmdlets do PowerShell neste tópico, a seguir é verdadeira:

* Todos os cmdlets seguintes são maiúsculas de minúsculas.
* O **– ErrorCategory PropertyConflict** sempre tem de ser incluída. Não existem atualmente não existem outros tipos de **ErrorCategory**, mas isso pode ser estendido no futuro.

Em primeiro lugar, começar executando **Connect-MsolService** e introduzir as credenciais de administrador inquilino.

Em seguida, utilize os seguintes cmdlets e operadores para ver os erros de formas diferentes:

1. [Ver todos](#see-all)
2. [Por tipo de propriedade](#by-property-type)
3. [Por valor em conflito](#by-conflicting-value)
4. [Utilizar uma pesquisa de cadeia de caracteres](#using-a-string-search)
5. Ordenados
6. [No todo ou numa quantidade limitada](#in-a-limited-quantity-or-all)

#### <a name="see-all"></a>Ver tudo
Assim que estiver ligado, para ver uma lista geral de aprovisionamento do atributo de erros no inquilino executam:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict`

Isso produz um resultado semelhante ao seguinte:  
 ![Get-MsolDirSyncProvisioningError](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/1.png "Get-MsolDirSyncProvisioningError")  

#### <a name="by-property-type"></a>Por tipo de propriedade
Para ver erros por tipo de propriedade, adicione a **- PropertyName** sinalizador com o **UserPrincipalName** ou **ProxyAddresses** argumento:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName UserPrincipalName`

Ou

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName ProxyAddresses`

#### <a name="by-conflicting-value"></a>Por valor em conflito
Para ver erros relacionados com uma propriedade específica, adicione a **- PropertyValue** sinalizador (**- PropertyName** tem de ser utilizada também ao adicionar este sinalizador):

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyValue User@domain.com -PropertyName UserPrincipalName`

#### <a name="using-a-string-search"></a>Utilizar uma pesquisa de cadeia de caracteres
Para fazer um uso de pesquisa de cadeia de caracteres amplo o **- SearchString** sinalizador. Isso pode ser usado independentemente de todos os sinalizadores acima, com exceção do **- ErrorCategory PropertyConflict**, sempre que é necessário:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -SearchString User`

#### <a name="in-a-limited-quantity-or-all"></a>No todo ou numa quantidade limitada
1. **MaxResults <Int>**  pode ser utilizado para limitar a consulta para um número específico de valores.
2. **Todos os** pode ser utilizado para garantir que todos os resultados são obtidos no caso de que existe um grande número de erros.

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -MaxResults 5`

## <a name="office-365-admin-portal"></a>Portal de administração do Office 365
Pode ver os erros de sincronização de diretório no Centro de administração do Office 365. O relatório no portal do Office 365 só é apresentado **utilizador** objetos que tenham estes erros. Esse teste não mostra informações sobre conflitos entre **grupos** e **contactos**.

![Utilizadores ativos](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/1234.png "utilizadores ativos")

Para obter instruções sobre como ver erros de sincronização de diretório no Centro de administração do Office 365, consulte [identificar erros de sincronização de diretório no Office 365](https://support.office.com/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067).

### <a name="identity-synchronization-error-report"></a>Relatório de erros de sincronização de identidade
Quando um objeto com um conflito de atributos duplicados é processado com esse novo comportamento de uma notificação é incluída no e-mail de relatório de erros de sincronização de identidade padrão que é enviado para a notificação técnica contacte para o inquilino. No entanto, há uma mudança importante nesse comportamento. No passado, as informações sobre um conflito de atributos duplicados seriam incluídas nesse cada relatório de erro subsequentes até que o conflito foi resolvido. Com esse novo comportamento, a notificação de erro para um determinado conflito só aparecer uma vez - no momento que o atributo em conflito foi colocado em quarentena.

Eis um exemplo do aspeto a notificação de e-mail para um conflito de ProxyAddress:  
    ![Utilizadores ativos](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/6.png "utilizadores ativos")  

## <a name="resolving-conflicts"></a>Resolver conflitos
Resolução de problemas e resolução de estratégia táticas quanto a erros não deve diferem da forma como os erros de atributos duplicados foram processados no passado. A única diferença é que a tarefa de temporizador varra por meio do inquilino no lado do serviço para adicionar automaticamente o atributo em questão para o objeto correto depois do conflito é resolvido.

O artigo seguinte descreve várias estratégias de resolução de problemas: [Atributos duplicados ou é inválidos impedem a sincronização de diretórios no Office 365](https://support.microsoft.com/kb/2647098).

## <a name="known-issues"></a>Problemas conhecidos
Nenhum desses problemas conhecidos faz com que dados perda ou degradação de serviços. Várias delas estão estética, outras pessoas fazer com que o padrão "*pré-resiliência*" erros de atributo duplicado para ser lançada em vez dos colocar em quarentena o atributo de conflito e outro faz com que determinados erros exigir extra manual de segurança de correção.

**Comportamento de núcleos:**

1. Objetos com configurações de atributo específico continuam a receber erros de exportação em vez dos atributos duplicados que está a ser colocados em quarentena.  
   Por exemplo:
   
    a. Novo utilizador é criado no AD com um UPN de **Joe\@contoso.com** e ProxyAddress **smtp:Joe\@contoso.com**
   
    b. As propriedades desse objeto em conflito com um grupo existente, onde é ProxyAddress **SMTP:Joe\@contoso.com**.
   
    c. No momento da exportação, um **ProxyAddress conflito** erro é apresentado em vez de ter os atributos de conflito colocados em quarentena. A operação será repetida em cada ciclo de sincronização, como teria sido antes da funcionalidade de resiliência foi ativada.
2. Se dois grupos são criados no local com o mesmo endereço SMTP, um deles falha a aprovisionar na primeira tentativa com um padrão duplicado **ProxyAddress** erro. No entanto, o valor duplicado corretamente foi colocado em quarentena após o próximo ciclo de sincronização.

**Relatório de Portal do Office**:

1. A mensagem de erro detalhada de dois objetos num conjunto de conflito UPN é o mesmo. Isto indica que eles têm ambos tinham respetivo UPN alterado / colocados em quarentena, quando na verdade apenas um deles tinha a qualquer alteração de dados.
2. A mensagem de erro detalhadas de um conflito UPN mostra o displayName errado para um utilizador que tenha tido o respetivo UPN alterado/em quarentena. Por exemplo:
   
    a. **O utilizador A** sincronizações primeiro com **UPN = User\@contoso.com**.
   
    b. **O utilizador B** é tentada a ser sincronizada, em seguida, com **UPN = User\@contoso.com**.
   
    c. **Utilizador de B** UPN é alterado para **User1234\@contoso.onmicrosoft.com** e **utilizador\@contoso.com** é adicionado ao **DirSyncProvisioningErrors** .
   
    d. A mensagem de erro para **utilizador B** deve indicar que **o utilizador A** já tem **utilizador\@contoso.com** como um UPN, mas mostra **do User B** displayName próprio.

**Relatório de erros de sincronização de identidade**:

A ligação para *passos sobre como resolver este problema* está incorreto:  
    ![Utilizadores ativos](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/6.png "utilizadores ativos")  

Ele deve apontar para [ https://aka.ms/duplicateattributeresiliency ](https://aka.ms/duplicateattributeresiliency).

## <a name="see-also"></a>Consulte também
* [Sincronização do Azure AD Connect](how-to-connect-sync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)
* [Identificar erros de sincronização de diretório no Office 365](https://support.office.com/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067)

