---
title: Sincronização de identidade e duplicação de atributos resiliência ; Microsoft Docs
description: Novo comportamento de como lidar com objetos com conflitos UPN ou ProxyAddress durante a sincronização de diretório utilizando o Azure AD Connect.
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
ms.openlocfilehash: 5585f0cd04dca4145f0322db9d625e35372b24b5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78298348"
---
# <a name="identity-synchronization-and-duplicate-attribute-resiliency"></a>Sincronização de identidades e resiliência de atributos duplicados
Duplicate Attribute Resiliency é uma funcionalidade no Diretório Ativo Azure que eliminará o atrito causado pelos conflitos **userprincipalName** e SMTP **ProxyAddress** ao executar uma das ferramentas de sincronização da Microsoft.

Estes dois atributos são geralmente obrigados a ser únicos em todos os **objetos user,** **Group,** ou **Contact** em um determinado inquilino do Diretório Ativo Azure.

> [!NOTE]
> Apenas os Utilizadores podem ter UPNs.
> 
> 

O novo comportamento que esta funcionalidade permite está na parte cloud do pipeline de sincronização, pelo que é agnóstico cliente e relevante para qualquer produto de sincronização da Microsoft, incluindo Azure AD Connect, DirSync e MIM + Connector. O termo genérico "cliente sincronizado" é usado neste documento para representar qualquer um destes produtos.

## <a name="current-behavior"></a>Comportamento atual
Se houver uma tentativa de fornecer um novo objeto com um valor UPN ou ProxyAddress que viole esta restrição de singularidade, o Azure Ative Directory bloqueia que o objeto seja criado. Da mesma forma, se um objeto for atualizado com uma UPN ou ProxyAddress não únicas, a atualização falha. A tentativa de provisionamento ou atualização é novamente tentada pelo cliente sincronizado em cada ciclo de exportação, e continua a falhar até que o conflito seja resolvido. Um e-mail de relatório de erro é gerado em cada tentativa e um erro é registado pelo cliente sincronizado.

## <a name="behavior-with-duplicate-attribute-resiliency"></a>Comportamento com Resistência do Atributo Duplicado
Em vez de não fornecer ou atualizar completamente um objeto com um atributo duplicado, o Diretório Ativo do Azure "põe em quarentena" o atributo duplicado que violaria a restrição de singularidade. Se este atributo for necessário para o fornecimento, como userPrincipalName, o serviço atribui um valor de espaço reservado. O formato destes valores temporários é  
OriginalPrefix>_**+\<4DigitNumber \@ \<>InitialTenantDomain>.onmicrosoft.com . \<**_

O processo de resiliência do atributo trata apenas dos valores UPN e SMTP **ProxyAddress.**

Se o atributo não for necessário, como um **ProxyAddress,** o Azure Ative Directory simplesmente põe em quarentena o atributo do conflito e procede à criação ou atualização do objeto.

Ao quarinar o atributo, a informação sobre o conflito é enviada no mesmo e-mail de relatório de erro usado no comportamento antigo. No entanto, esta informação só aparece no relatório de erro uma vez, quando a quarentena acontece, não continua a ser registada em futuros e-mails. Além disso, uma vez que a exportação para este objeto foi bem sucedida, o cliente sincronizado não regista um erro e não volta a tentar o funcionamento de criação/atualização nos ciclos de sincronização subsequentes.

Para suportar este comportamento foi adicionado um novo atributo às classes de objetos de utilizador, grupo e contacto:  
**Erros de fornecimento dirSync**

Este é um atributo multi-valorizado que é usado para armazenar os atributos conflituosos que violariam a restrição de singularidade se fossem adicionados normalmente. Uma tarefa temporizador de fundo foi ativada no Diretório Ativo Azure que funciona de hora a hora para procurar por conflitos de atributos duplicados que foram resolvidos, e remove automaticamente os atributos em questão da quarentena.

### <a name="enabling-duplicate-attribute-resiliency"></a>Habilitar a resistência ao atributo duplicado
Duplicar a Resiliência do Atributo será o novo comportamento padrão em todos os inquilinos do Azure Ative Directory. Será por defeito para todos os inquilinos que permitiram a sincronização pela primeira vez no dia 22 de agosto de 2016 ou mais tarde. Os inquilinos que ativaram a sincronização antes desta data terão a funcionalidade ativada em lotes. Este lançamento começará em setembro de 2016 e será enviada uma notificação por e-mail para o contacto técnico de notificação de cada inquilino com a data específica em que a funcionalidade será ativada.

> [!NOTE]
> Uma vez ligada a Resistência do Atributo Duplicate, não pode ser desativada.

Para verificar se a funcionalidade está ativada para o seu inquilino, pode fazê-lo baixando a versão mais recente do módulo PowerShell do Diretório Ativo Azure e executando:

`Get-MsolDirSyncFeatures -Feature DuplicateUPNResiliency`

`Get-MsolDirSyncFeatures -Feature DuplicateProxyAddressResiliency`

> [!NOTE]
> Já não pode utilizar o cmdlet Set-MsolDirSyncFeature para ativar proactivamente a funcionalidade de resiliência do Atributo Duplicate antes de ser ativada para o seu inquilino. Para poder testar a funcionalidade, terá de criar um novo inquilino do Azure Ative Directory.

## <a name="identifying-objects-with-dirsyncprovisioningerrors"></a>Identificação de Objetos com DirSyncProvisioningErrors
Existem atualmente dois métodos para identificar objetos que têm estes erros devido a duplicados conflitos de propriedades, O Diretório Ativo Azure PowerShell e o centro de administração microsoft [365](https://admin.microsoft.com). Há planos para estender a informação adicional baseada em portal no futuro.

### <a name="azure-active-directory-powershell"></a>Azure Active Directory do PowerShell
Para os cmdlets PowerShell neste tópico, o seguinte é verdade:

* Todos os seguintes cmdlets são sensíveis ao caso.
* O **–ErrorCategory PropertyConflict** deve ser sempre incluído. Atualmente, não existem outros tipos de Categoria de **Erro,** mas este poderá ser alargado no futuro.

Primeiro, começar por executar **o Connect-MsolService** e introduzir credenciais para um administrador de inquilinos.

Em seguida, utilize os seguintes cmdlets e operadores para ver erros de diferentes formas:

1. [Ver Tudo](#see-all)
2. [Por Tipo de Propriedade](#by-property-type)
3. [Por valor conflituoso](#by-conflicting-value)
4. [Usando uma pesquisa de cordas](#using-a-string-search)
5. Ordenado
6. [Em uma quantidade limitada ou tudo](#in-a-limited-quantity-or-all)

#### <a name="see-all"></a>Ver todos
Uma vez ligado, para ver uma lista geral de erros de provisão de atributos na execução do arrendatário:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict`

Isto produz um resultado como o seguinte:  
 ![Get-MsolDirSyncProvisioningError](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/1.png "Get-MsolDirSyncProvisioningError")  

#### <a name="by-property-type"></a>Por tipo de propriedade
Para ver erros por tipo de propriedade, adicione a bandeira **-PropertyName** com o nome **do utilizador principal** ou argumento **proxyAddresss:**

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName UserPrincipalName`

Ou

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName ProxyAddresses`

#### <a name="by-conflicting-value"></a>Por valor conflituoso
Para ver erros relacionados com uma propriedade específica, adicione a bandeira **-PropertyValue** **(-PropertyName** também deve ser usado ao adicionar esta bandeira):

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyValue User@domain.com -PropertyName UserPrincipalName`

#### <a name="using-a-string-search"></a>Usando uma pesquisa de cordas
Para fazer uma pesquisa de cordas largas, use a bandeira **-SearchString.** Isto pode ser usado independentemente de todas as bandeiras acima referidas, com exceção de **-ErrorCategory PropertyConflict**, que é sempre necessário:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -SearchString User`

#### <a name="in-a-limited-quantity-or-all"></a>Em uma quantidade limitada ou todos
1. **MaxResults \<Int>** pode ser usado para limitar a consulta a um número específico de valores.
2. **Tudo** pode ser usado para garantir que todos os resultados são recuperados no caso de existir um grande número de erros.

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -MaxResults 5`

## <a name="microsoft-365-admin-center"></a>Centro de administração do Microsoft 365
Pode ver erros de sincronização de diretórios no centro de administração da Microsoft 365. O relatório no centro de administração da Microsoft 365 apenas exibe objetos **de utilizador** que tenham estes erros. Não mostra informações sobre conflitos entre **Grupos** e **Contactos.**

![Utilizadores Ativos](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/1234.png "Utilizadores Ativos")

Para obter instruções sobre como ver erros de sincronização de diretórios no centro de administração da Microsoft 365, consulte Identificar erros de sincronização de [diretórios no Office 365](https://support.office.com/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067).

### <a name="identity-synchronization-error-report"></a>Relatório de erro de sincronização de identidade
Quando um objeto com um conflito de atributos duplicado é tratado com este novo comportamento, uma notificação é incluída no e-mail padrão do Relatório de Erro de Sincronização de Identidade que é enviado para o contacto de Notificação Técnica para o inquilino. No entanto, há uma mudança importante neste comportamento. No passado, as informações sobre um conflito de atributos duplicados seriam incluídas em todos os relatórios de erro subsequentes até que o conflito fosse resolvido. Com este novo comportamento, a notificação de erro para um dado conflito só aparece uma vez, no momento em que o atributo conflituoso está em quarentena.

Aqui está um exemplo do que a notificação de e-mail parece para um conflito ProxyAddress:  
    ![Utilizadores Ativos](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/6.png "Utilizadores Ativos")  

## <a name="resolving-conflicts"></a>Resolução de conflitos
As táticas de estratégia e resolução de resolução de problemas para estes erros não devem diferir da forma como os erros de atribuição duplicados foram tratados no passado. A única diferença é que a tarefa do temporizador passa pelo inquilino do lado do serviço para adicionar automaticamente o atributo em questão ao objeto adequado uma vez resolvido o conflito.

O seguinte artigo descreve várias estratégias de resolução e resolução de problemas: [Atributos duplicados ou inválidos impedem a sincronização do diretório no Office 365](https://support.microsoft.com/kb/2647098).

## <a name="known-issues"></a>Problemas conhecidos
Nenhuma destas questões conhecidas causa perda de dados ou degradação do serviço. Vários deles são estéticos, outros causam erros de atribuição de duplicados padrão "*pré-resiliência*" a serem lançados em vez de quarentenar o atributo do conflito, e outro causa certos erros para exigir uma correção manual extra.

**Comportamento do núcleo:**

1. Os objetos com configurações específicas do atributo continuam a receber erros de exportação em oposição ao ou atributo duplicado em quarentena.  
   Por exemplo:
   
    a. Novo utilizador é criado em AD com uma UPN de **Joe\@contoso.com** e ProxyAddress **\@smtp:Joe contoso.com**
   
    b. As propriedades deste objeto entram em conflito com um grupo existente, onde proxyAddress é **SMTP:Joe\@contoso.com**.
   
    c. Após a exportação, um erro de **conflito proxyAddress** é lançado em vez de ter os atributos de conflito em quarentena. A operação é novamente experimentada em cada ciclo de sincronização subsequente, como teria sido antes da função de resiliência ter sido ativada.
2. Se dois grupos forem criados no local com o mesmo endereço SMTP, não se prevê a primeira tentativa com um erro **proxyAddress** duplicado padrão. No entanto, o valor duplicado está devidamente em quarentena no próximo ciclo de sincronização.

**Relatório do Portal do Escritório:**

1. A mensagem de erro detalhada para dois objetos num conjunto de conflitos da UPN é a mesma. Isto indica que ambos tiveram a sua UPN alterada/quarentena, quando na verdade apenas um deles tinha qualquer alteração de dados.
2. A mensagem de erro detalhada para um conflito UPN mostra o nome de exibição errado para um utilizador que teve a sua UPN alterada/quarentena. Por exemplo:
   
    a. **Utilizador A** sincroniza-se primeiro com **UPN\@= utilizador contoso.com**.
   
    b. **O utilizador B** é tentado a ser sincronizado a seguir com **UPN =\@Utilizador contoso.com**.
   
    c. **Utilizadores B's** A UPN é alterada para **User1234\@contoso.onmicrosoft.com** e **user\@contoso.com** é adicionado ao **DirSyncProvisioningErrors**.
   
    d. A mensagem de erro do **Utilizador B** deve indicar que o **Utilizador A** já tem contoso.com **utilizador\@** como UPN, mas mostra o próprio nome de visualização **do Utilizador B.**

**Relatório de erro**de sincronização de identidade:

O link para *as etapas sobre a forma de resolver esta questão* é incorreto:  
    ![Utilizadores Ativos](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/6.png "Utilizadores Ativos")  

Deve apontar [https://aka.ms/duplicateattributeresiliency](https://aka.ms/duplicateattributeresiliency)para.

## <a name="see-also"></a>Consulte também
* [Sincronização azure AD Connect](how-to-connect-sync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)
* [Identificar erros de sincronização de diretório sincronia no Office 365](https://support.office.com/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067)

