---
title: Resolver problemas de atribuição de licença para um grupo-Azure Active Directory | Microsoft Docs
description: Como identificar e resolver problemas de atribuição de licença quando você estiver usando Azure Active Directory licenciamento baseado em grupo
services: active-directory
keywords: Licenciamento do Azure AD
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 09/23/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 247dee2cfbb00b185e941fde05c2198459a05e20
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73815746"
---
# <a name="identify-and-resolve-license-assignment-problems-for-a-group-in-azure-active-directory"></a>Identificar e resolver problemas de atribuição de licença para um grupo no Azure Active Directory

O licenciamento baseado em grupo no Azure Active Directory (Azure AD) apresenta o conceito de usuários em um estado de erro de licenciamento. Neste artigo, explicamos os motivos pelos quais os usuários podem terminar nesse estado.

Quando você atribui licenças diretamente a usuários individuais, sem usar o licenciamento baseado em grupo, a operação de atribuição pode falhar. Por exemplo, quando você executa o cmdlet do PowerShell `Set-MsolUserLicense` em um sistema de usuário, o cmdlet pode falhar por vários motivos relacionados à lógica de negócios. Por exemplo, pode haver um número insuficiente de licenças ou um conflito entre dois planos de serviço que não podem ser atribuídos ao mesmo tempo. O problema é imediatamente relatado de volta para você.

Quando você estiver usando o licenciamento baseado em grupo, os mesmos erros poderão ocorrer, mas ocorrerão em segundo plano enquanto o serviço do Azure AD estiver atribuindo licenças. Por esse motivo, os erros não podem ser comunicados a você imediatamente. Em vez disso, eles são registrados no objeto de usuário e, em seguida, relatados por meio do portal administrativo. A intenção original de licenciar o usuário nunca é perdida, mas é registrada em um estado de erro para investigação e resolução futuras.

## <a name="licenseassignmentattributeconcurrencyexception-in-audit-logs"></a>LicenseAssignmentAttributeConcurrencyException em logs de auditoria

**Problema:** O usuário tem LicenseAssignmentAttributeConcurrencyException para a atribuição de licença nos logs de auditoria.
Quando o licenciamento baseado em grupo tenta processar a atribuição de licença simultânea da mesma licença a um usuário, essa exceção é registrada no usuário. Isso geralmente acontece quando um usuário é membro de mais de um grupo com a mesma licença atribuída. O AZure AD tentará processar novamente a licença de usuário e resolverá o problema. Não é necessária nenhuma ação do cliente para corrigir esse problema.

## <a name="find-license-assignment-errors"></a>Localizar erros de atribuição de licença

### <a name="to-find-users-in-an-error-state-in-a-group"></a>Para localizar usuários em um estado de erro em um grupo

1. Abra o grupo em sua página de visão geral e selecione **licenças**. Uma notificação será exibida se houver usuários em um estado de erro.

   ![Mensagem de notificação de erro e grupo](./media/licensing-groups-resolve-problems/group-error-notification.png)

1. Selecione a notificação para abrir uma lista de todos os usuários afetados. Você pode selecionar cada usuário individualmente para ver mais detalhes.

   ![lista de usuários no estado de erro de licenciamento de grupo](./media/licensing-groups-resolve-problems/list-of-users-with-errors.png)

1. Para localizar todos os grupos que contêm pelo menos um erro, na folha **Azure Active Directory** , selecione **licenças**e, em seguida, selecione **visão geral**. Uma caixa de informações é exibida quando os grupos exigem sua atenção.

   ![Visão geral e informações sobre grupos em estado de erro](./media/licensing-groups-resolve-problems/group-errors-widget.png)

1. Marque a caixa para ver uma lista de todos os grupos com erros. Você pode selecionar cada grupo para obter mais detalhes.

   ![Visão geral e lista de grupos com erros](./media/licensing-groups-resolve-problems/list-of-groups-with-errors.png)

As seções a seguir fornecem uma descrição de cada possível problema e a maneira de resolvê-lo.

## <a name="not-enough-licenses"></a>Não há licenças suficientes

**Problema:** Não há licenças suficientes disponíveis para um dos produtos especificados no grupo. Você precisa comprar mais licenças para o produto ou liberar licenças não utilizadas de outros usuários ou grupos.

Para ver quantas licenças estão disponíveis, acesse **Azure Active Directory** **licenças** de >  > **todos os produtos**.

Para ver quais usuários e grupos estão consumindo licenças, selecione um produto. Em **usuários licenciados**, você verá uma lista de todos os usuários que tiveram licenças atribuídas diretamente ou por meio de um ou mais grupos. Em **grupos licenciados**, você verá todos os grupos que têm os produtos atribuídos.

**PowerShell:** Os cmdlets do PowerShell relatam esse erro como _CountViolation_.

## <a name="conflicting-service-plans"></a>Planos de serviço conflitantes

**Problema:** Um dos produtos especificados no grupo contém um plano de serviço que está em conflito com outro plano de serviço que já está atribuído ao usuário por meio de um produto diferente. Alguns planos de serviço são configurados de forma que não possam ser atribuídos ao mesmo usuário que outro plano de serviço relacionado.

Considere o seguinte exemplo. Um usuário tem uma licença do Office 365 Enterprise *E1* atribuída diretamente, com todos os planos habilitados. O usuário foi adicionado a um grupo que tem o produto Office 365 Enterprise *E3* atribuído a ele. O produto E3 contém planos de serviço que não podem se sobrepor aos planos incluídos no E1, portanto, a atribuição de licença de grupo falha com o erro "planos de serviço conflitantes". Neste exemplo, os planos de serviço conflitantes são:

- O SharePoint Online (plano 2) está em conflito com o SharePoint Online (plano 1).
- O Exchange Online (plano 2) está em conflito com o Exchange Online (plano 1).

Para resolver esse conflito, você precisa desabilitar dois dos planos. Você pode desabilitar a licença E1 que está diretamente atribuída ao usuário. Ou, você precisa modificar a atribuição de licença de grupo inteira e desabilitar os planos na licença E3. Como alternativa, você pode optar por remover a licença E1 do usuário se ele for redundante no contexto da licença E3.

A decisão sobre como resolver licenças de produtos conflitantes sempre pertence ao administrador. O Azure AD não resolve automaticamente os conflitos de licença.

**PowerShell:** Os cmdlets do PowerShell relatam esse erro como _MutuallyExclusiveViolation_.

## <a name="other-products-depend-on-this-license"></a>Outros produtos dependem desta licença

**Problema:** Um dos produtos especificados no grupo contém um plano de serviço que deve ser habilitado para outro plano de serviço, em outro produto, para funcionar. Esse erro ocorre quando o Azure AD tenta remover o plano de serviço subjacente. Por exemplo, isso pode acontecer quando você remove o usuário do grupo.

Para resolver esse problema, você precisa verificar se o plano necessário ainda está atribuído aos usuários por meio de algum outro método ou se os serviços dependentes estão desabilitados para esses usuários. Depois de fazer isso, você pode remover corretamente a licença de grupo desses usuários.

**PowerShell:** Os cmdlets do PowerShell relatam esse erro como _DependencyViolation_.

## <a name="usage-location-isnt-allowed"></a>O local de uso não é permitido

**Problema:** Alguns serviços da Microsoft não estão disponíveis em todos os locais devido a leis e regulamentos locais. Antes de atribuir uma licença a um usuário, você deve especificar a propriedade **local de uso** para o usuário. Você pode especificar o local na seção **configurações** do **usuário** > **perfil** > no portal do Azure.

Quando o Azure AD tenta atribuir uma licença de grupo a um usuário cujo local de uso não tem suporte, ele falha e registra um erro no usuário.

Para resolver esse problema, remova usuários de locais sem suporte do grupo licenciado. Como alternativa, se os valores de local de uso atual não representarem o local de usuário real, você poderá modificá-los para que as licenças sejam atribuídas corretamente na próxima vez (se houver suporte para o novo local).

**PowerShell:** Os cmdlets do PowerShell relatam esse erro como _ProhibitedInUsageLocationViolation_.

> [!NOTE]
> Quando o Azure AD atribui licenças de grupo, todos os usuários sem um local de uso especificado herdam o local do diretório. Recomendamos que os administradores definam os valores corretos de local de uso nos usuários antes de usar o licenciamento baseado em grupo para obedecer às leis e regulamentos locais.

## <a name="duplicate-proxy-addresses"></a>Endereços de proxy duplicados

Se você usar o Exchange Online, alguns usuários em seu locatário poderão estar configurados incorretamente com o mesmo valor de endereço de proxy. Quando o licenciamento baseado em grupo tenta atribuir uma licença a tal usuário, ele falha e mostra "o endereço de proxy já está sendo usado".

> [!TIP]
> Para ver se há um endereço de proxy duplicado, execute o seguinte cmdlet do PowerShell no Exchange Online:
> ```
> Get-Recipient -ResultSize unlimited | where {$_.EmailAddresses -match "user@contoso.onmicrosoft.com"} | fL Name, RecipientType,emailaddresses
> ```
> Para obter mais informações sobre esse problema, consulte a [mensagem de erro "o endereço proxy já está sendo usado" no Exchange Online](https://support.microsoft.com/help/3042584/-proxy-address-address-is-already-being-used-error-message-in-exchange-online). O artigo também inclui informações sobre [como se conectar ao Exchange Online usando o PowerShell remoto](https://technet.microsoft.com/library/jj984289.aspx).

Depois de resolver qualquer problema de endereço de proxy para os usuários afetados, certifique-se de forçar o processamento de licença no grupo para certificar-se de que as licenças agora podem ser aplicadas.

## <a name="azure-ad-mail-and-proxyaddresses-attribute-change"></a>Alteração de atributo de ProxyAddresses e de email do Azure AD

**Problema:** Ao atualizar a atribuição de licença em um usuário ou grupo, você pode ver que o atributo email e ProxyAddresses do Azure AD de alguns usuários são alterados.

Atualizar a atribuição de licença em um usuário faz com que o cálculo do endereço proxy seja disparado, o que pode alterar os atributos do usuário. Para entender o motivo exato da alteração e resolver o problema, consulte este artigo sobre [como o atributo proxyAddresses é populado no Azure ad](https://support.microsoft.com/help/3190357/how-the-proxyaddresses-attribute-is-populated-in-azure-ad).

## <a name="more-than-one-product-license-assigned-to-a-group"></a>Mais de uma licença de produto atribuída a um grupo

Você pode atribuir mais de uma licença de produto a um grupo. Por exemplo, você pode atribuir o Office 365 Enterprise E3 e Enterprise Mobility + Security a um grupo para habilitar facilmente todos os serviços incluídos para os usuários.

O Azure AD tenta atribuir todas as licenças especificadas no grupo para cada usuário. Se o Azure AD não puder atribuir um dos produtos devido a problemas de lógica de negócios, ele também não atribuirá as outras licenças no grupo. Um exemplo é se não houver licenças suficientes para todos ou se houver conflitos com outros serviços habilitados no usuário.

Você pode ver os usuários que não puderam ser atribuídos e verificar quais produtos são afetados por esse problema.

## <a name="when-a-licensed-group-is-deleted"></a>Quando um grupo licenciado é excluído

Você deve remover todas as licenças atribuídas a um grupo antes de poder excluir o grupo. No entanto, a remoção de licenças de todos os usuários no grupo pode levar tempo. Ao remover as atribuições de licença de um grupo, pode haver falhas se o usuário tiver uma licença dependente atribuída ou se houver um problema de conflito de endereço de proxy que proíba a remoção da licença. Se um usuário tiver uma licença que seja dependente de uma licença que está sendo removida devido a uma exclusão de grupo, a atribuição de licença ao usuário será convertida de herdada para direta.

Por exemplo, considere um grupo que tenha o Office 365 E3/E5 atribuído com um plano de serviço do Skype for Business habilitado. Imagine também que alguns membros do grupo tenham licenças de conferência de áudio atribuídas diretamente. Quando o grupo for excluído, o licenciamento baseado em grupo tentará remover o Office 365 E3/E5 de todos os usuários. Como a conferência de áudio depende do Skype for Business, para todos os usuários com a conferência de áudio atribuída, o licenciamento baseado em grupo converte as licenças do Office 365 E3/E5 para direcionar a atribuição de licença.

## <a name="manage-licenses-for-products-with-prerequisites"></a>Gerenciar licenças para produtos com pré-requisitos

Alguns produtos online da Microsoft que você pode ter são *Complementos.* Os complementos exigem que um plano de serviço de pré-requisito seja habilitado para um usuário ou grupo antes que possa receber uma licença. Com o licenciamento baseado em grupo, o sistema requer que ambos os planos de serviço de pré-requisito e complemento estejam presentes no mesmo grupo. Isso é feito para garantir que todos os usuários que forem adicionados ao grupo possam receber o produto totalmente funcional. Vamos considerar o exemplo a seguir:

O Microsoft Workplace Analytics é um produto complementar. Ele contém um único plano de serviço com o mesmo nome. Só podemos atribuir esse plano de serviço a um usuário, ou grupo, quando um dos seguintes pré-requisitos também for atribuído:

- Exchange Online (plano 1)
- Exchange Online (plano 2)

Se tentarmos atribuir este produto por conta própria a um grupo, o portal retornará uma mensagem de notificação. Se selecionarmos os detalhes do item, ele mostrará a seguinte mensagem de erro:

  "Falha na operação de licença. Verifique se o grupo tem os serviços necessários antes de adicionar ou remover um serviço dependente. **O serviço Microsoft Workplace Analytics requer o Exchange Online (plano 2) para ser habilitado também.** "

Para atribuir essa licença de complemento a um grupo, devemos garantir que o grupo também contenha o plano de serviço de pré-requisito. Por exemplo, poderemos atualizar um grupo existente que já contenha o produto Office 365 E3 completo e, em seguida, adicionar o produto complementar a ele.

Também é possível criar um grupo autônomo que contenha apenas os produtos mínimos necessários para que o complemento funcione. Ele pode ser usado para licenciar apenas os usuários selecionados para o produto de complemento. Com base no exemplo anterior, você atribuiria os seguintes produtos ao mesmo grupo:

- Office 365 Enterprise E3 com apenas o plano de serviço do Exchange Online (plano 2) habilitado
- Microsoft Workplace Analytics

De agora em diante, todos os usuários adicionados a esse grupo consomem uma licença do produto E3 e uma licença do produto Workplace Analytics. Ao mesmo tempo, esses usuários podem ser membros de outro grupo que fornece a eles o produto E3 completo e ainda consomem apenas uma licença para esse produto.

> [!TIP]
> Você pode criar vários grupos para cada plano de serviço de pré-requisito. Por exemplo, se você usar o Office 365 Enterprise E1 e o Office 365 Enterprise E3 para seus usuários, poderá criar dois grupos para licenciar o Microsoft Workplace Analytics: um que usa o E1 como um pré-requisito e o outro que usa E3. Isso permite que você distribua o complemento para os usuários E1 e E3 sem consumir licenças adicionais.

## <a name="force-group-license-processing-to-resolve-errors"></a>Forçar o processamento de licença de grupo para resolver erros

Dependendo de quais etapas você executou para resolver os erros, pode ser necessário disparar manualmente o processamento de um grupo para atualizar o estado do usuário.

Por exemplo, se você liberar algumas licenças removendo atribuições de licenças diretas dos usuários, será necessário disparar o processamento de grupos que anteriormente falharam ao licenciar totalmente todos os membros do usuário. Para reprocessar um grupo, vá para o painel grupo, abra **licenças**e, em seguida, selecione o botão **Reprocessar** na barra de ferramentas.

## <a name="force-user-license-processing-to-resolve-errors"></a>Forçar o processamento de licença de usuário para resolver erros

Dependendo de quais etapas você executou para resolver os erros, pode ser necessário disparar manualmente o processamento de um usuário para atualizar o estado dos usuários.

Por exemplo, depois de resolver o problema de endereço de proxy duplicado para um usuário afetado, você precisa disparar o processamento do usuário. Para reprocessar um usuário, vá para o painel usuário, abra **licenças**e, em seguida, selecione o botão **Reprocessar** na barra de ferramentas.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre outros cenários de gerenciamento de licenças por meio de grupos, consulte o seguinte:

* [O que é o licenciamento baseado em grupo no Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
* [Atribuir licenças a um grupo no Azure Active Directory](licensing-groups-assign.md)
* [Como migrar os utilizadores licenciados individuais para o licenciamento baseado no grupo no Azure Active Directory](licensing-groups-migrate-users.md)
* [Como migrar usuários entre licenças de produto usando o licenciamento baseado em grupo no Azure Active Directory](licensing-groups-change-licenses.md)
* [Cenários adicionais de licenciamento baseado no grupo do Azure Active Directory](licensing-group-advanced.md)
* [Exemplos do PowerShell para licenciamento baseado em grupo no Azure Active Directory](licensing-ps-examples.md)
