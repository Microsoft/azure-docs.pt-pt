---
title: Resolver problemas de atribuição de licença para um grupo - Azure Active Directory | Documentos da Microsoft
description: Como identificar e resolver problemas de atribuição de licenças quando estiver a utilizar o Azure Active Directory com base no grupo de licenciamento
services: active-directory
keywords: Licenciamento do Azure AD
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c92969015910cc5bd72e2d9339d5c15c1f7af48b
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58201539"
---
# <a name="identify-and-resolve-license-assignment-problems-for-a-group-in-azure-active-directory"></a>Identificar e resolver problemas de atribuição de licença para um grupo no Azure Active Directory

Com base no grupo de licenciamento no Azure Active Directory (Azure AD) introduz o conceito de usuários num Estado de erro de licenciamento. Neste artigo, vamos explicar os motivos por que os utilizadores podem acabar neste estado.

Quando atribuir licenças a diretamente a utilizadores individuais, sem utilizar o licenciamento baseado em grupo, a operação de atribuição poderá falhar. Por exemplo, quando executar o cmdlet do PowerShell `Set-MsolUserLicense` num sistema de utilizador, o cmdlet pode falhar por muitos motivos relacionados com a lógica de negócios. Por exemplo, pode ser um número insuficiente de licenças ou um conflito entre dois planos de serviço que não podem ser atribuídos ao mesmo tempo. O problema imediatamente é informado de volta para si.

Quando estiver a utilizar com base no grupo de licenciamento, os mesmos erros podem ocorrer, mas ocorrerem em segundo plano, ao mesmo tempo o serviço do Azure AD é atribuir licenças. Por esse motivo, os erros não podem ser comunicados imediatamente. Em vez disso, estão registados no objeto user e, em seguida, são comunicados através do portal administrativo. A intenção original para licenciar o usuário nunca é perdida, mas ele será gravado num Estado de erro para investigação futura e resolução.

## <a name="how-to-find-license-assignment-errors"></a>Como encontrar erros de atribuição de licença
**Para localizar erros de atribuição de licença**

1. Para localizar utilizadores no Estado com erros num grupo específico, abra o painel para o grupo. Sob **licenças**, é apresentada uma notificação se existirem quaisquer utilizadores no Estado com erros.

   ![Mensagem de notificações de grupo e erro](./media/licensing-groups-resolve-problems/group-error-notification.png)

2. Selecione a notificação para abrir uma lista de todos os utilizadores afetados. Pode selecionar cada utilizador individualmente para ver mais detalhes.

   ![lista de utilizadores no grupo de licenciamento de estado de erro](./media/licensing-groups-resolve-problems/list-of-users-with-errors.png)

3. Para localizar todos os grupos que contêm pelo menos um erro, no **do Azure Active Directory** selecione painel **licenças**e, em seguida, selecione **descrição geral**. É apresentada uma caixa de informações quando grupos necessitam da sua atenção.

   ![Descrição geral e informações sobre os grupos no Estado com erros](./media/licensing-groups-resolve-problems/group-errors-widget.png)

4. Selecione a caixa para ver uma lista de todos os grupos com erros. Pode selecionar cada grupo para obter mais detalhes.

   ![Descrição geral e a lista de grupos com erros](./media/licensing-groups-resolve-problems/list-of-groups-with-errors.png)


As secções seguintes fornecem uma descrição de cada problema em potencial e a forma de resolvê-lo.

## <a name="not-enough-licenses"></a>Não existem licenças suficientes

**Problema:** Não existem licenças suficientes disponíveis para um dos produtos que é especificado no grupo. Tem de adquirir mais licenças para o produto ou libertar a partir de outros utilizadores ou grupos de licenças por utilizar.

Para ver quantas licenças estão disponíveis, aceda a **do Azure Active Directory** > **licenças** > **todos os produtos**.

Para ver a quais usuários e grupos que estão consumindo licenças, selecione um produto. Sob **utilizadores com licença**, verá uma lista de todos os utilizadores que tenham tido licenças atribuídas diretamente ou através de um ou mais grupos. Sob **licenciado grupos**, verá todos os grupos que têm esses produtos atribuído.

**PowerShell:** Cmdlets do PowerShell comunique este problema, como _CountViolation_.

## <a name="conflicting-service-plans"></a>Planos de serviço em conflito

**Problema:** Um dos produtos que é especificado no grupo contém um plano do serviço que está em conflito com outro plano do serviço que já está atribuído ao utilizador através de um produto diferente. Alguns planos de serviço são configurados de forma que não é possível atribuir o mesmo utilizador como o plano do serviço de outra, relacionados.

Considere o seguinte exemplo. Um utilizador tiver uma licença para o Office 365 Enterprise *E1* atribuídos diretamente, com todos os planos ativados. O utilizador foi adicionado a um grupo que tenha o Office 365 Enterprise *E3* produto atribuído ao mesmo. O produto E3 contém planos de serviço que não podem sobrepor-se com os planos que estão incluídos no E1, para que a atribuição de licença de grupo falha com o erro "Planos do serviço em conflito". Neste exemplo, os planos de serviço em conflito são:

-   SharePoint Online (plano 2) está em conflito com o SharePoint Online (plano 1).
-   Exchange Online (plano 2) está em conflito com o Exchange Online (plano 1).

Para resolver este conflito, terá de desativar a dois dos planos. Pode desativar a licença E1, que lhe esteja diretamente atribuída ao utilizador. Em alternativa, precisa modificar a atribuição de licenças do grupo inteiro e desative os planos na licença E3. Em alternativa, pode optar por remover a licença de E1 do usuário se ele é redundante no contexto da licença E3.

A decisão sobre como resolver em conflito licenças de produtos sempre pertence ao administrador. O Azure AD não resolve automaticamente os conflitos de licença.

**PowerShell:** Cmdlets do PowerShell comunique este problema, como _MutuallyExclusiveViolation_.

## <a name="other-products-depend-on-this-license"></a>Outros produtos dependem desta licença

**Problema:** Um dos produtos que é especificado no grupo contém um plano de serviço que tem de estar ativado para outro plano do serviço, no outro produto, a função. Este erro ocorre quando tenta remover o plano do serviço subjacente do Azure AD. Por exemplo, isto pode acontecer quando remover o utilizador do grupo.

Para resolver este problema, terá de certificar-se de que o plano necessário ainda se encontrar atribuído aos utilizadores através de outro método ou que os serviços dependentes estão desativados para esses utilizadores. Depois de fazer isso, pode remover corretamente a licença de grupo aos utilizadores.

**PowerShell:** Cmdlets do PowerShell comunique este problema, como _DependencyViolation_.

## <a name="usage-location-isnt-allowed"></a>Não é permitida a localização de utilização

**Problema:** Alguns serviços da Microsoft não estão disponíveis em todas as localizações devido às leis e regulamentações locais. Antes de poder atribuir uma licença a um utilizador, tem de especificar o **localização de utilização** propriedade para o utilizador. Pode especificar a localização no **usuário** > **perfil** > **definições** secção no portal do Azure.

Quando tenta do Azure AD atribuir uma licença de grupo para um utilizador cuja localização de utilização não é suportada, falha e regista um erro no utilizador.

Para resolver este problema, remova os utilizadores de nonsupported locais do grupo de licenciado. Em alternativa, se os valores de localização de utilização atuais não representam a localização de utilizador real, pode modificá-los para que as licenças são atribuídas corretamente a próxima vez que (se o novo local é suportado).

**PowerShell:** Cmdlets do PowerShell comunique este problema, como _ProhibitedInUsageLocationViolation_.

> [!NOTE]
> Quando o Azure AD atribuir licenças de grupo, todos os utilizadores sem uma localização de utilização especificada herdam a localização do diretório. Recomendamos que os administradores definir a correta utilização valores de localização em utilizadores antes de utilizar o licenciamento baseado em grupo para cumprir as leis e regulamentações locais.

## <a name="duplicate-proxy-addresses"></a>Endereços de proxy duplicados

Se utilizar o Exchange Online, alguns utilizadores no seu inquilino podem ser incorretamente configurados com o mesmo valor de endereço de proxy. Quando tenta com o licenciamento baseado em grupo atribuir uma licença para esse usuário, falhará e mostra "endereço de Proxy já está a ser utilizado".

> [!TIP]
> Para ver se existe um endereço de proxy duplicados, execute o seguinte cmdlet do PowerShell no Exchange Online:
> ```
> Run Get-Recipient | where {$_.EmailAddresses -match "user@contoso.onmicrosoft.com"} | fL Name, RecipientType,emailaddresses
> ```
> Para obter mais informações sobre este problema, consulte [mensagem de erro "o endereço de Proxy já está a ser utilizado" no Exchange Online](https://support.microsoft.com/help/3042584/-proxy-address-address-is-already-being-used-error-message-in-exchange-online). O artigo também inclui informações sobre [como ligar ao Exchange Online com o PowerShell remoto](https://technet.microsoft.com/library/jj984289.aspx). Veja este artigo para obter mais informações [sobre como o atributo proxyAddresses é preenchido no Azure AD](https://support.microsoft.com/help/3190357/how-the-proxyaddresses-attribute-is-populated-in-azure-ad).

Depois de resolver quaisquer problemas de endereço de proxy para os utilizadores afetados, certifique-se forçar o processamento de licença no grupo de certificar-se de que as licenças agora podem ser aplicadas.

## <a name="what-happens-when-theres-more-than-one-product-license-on-a-group"></a>O que acontece quando existe mais de uma licença de produto num grupo?

Pode atribuir mais de uma licença de produto para um grupo. Por exemplo, pode atribuir o Office 365 Enterprise E3 e o Enterprise Mobility + Security a um grupo para ativar facilmente serviços incluídos para os utilizadores.

Tenta do Azure AD atribuir a todas as licenças que estão especificadas no grupo de cada utilizador. Se do Azure AD não é possível atribuir um dos produtos devido a problemas de lógica de negócios, ele só serão atribuídas as outras licenças no grupo de optar por. Um exemplo é se não existem licenças suficientes para todos, ou se existirem conflitos com outros serviços que estão ativados no utilizador.

Pode ver os utilizadores que não foi possível obter atribuído e verifique quais os produtos são afetados por este problema.

## <a name="what-happens-when-a-group-with-licenses-assigned-is-deleted"></a>O que acontece quando um grupo com licenças atribuídas é eliminado?

Tem de remover todas as licenças atribuídas a um grupo antes de poder eliminar o grupo. No entanto, remover licenças de todos os utilizadores no grupo pode demorar tempo. Ao remover atribuições de licenças de um grupo, pode haver falhas se o utilizador tem atribuída uma licença dependente ou se existe um problema de conflito de endereços de proxy que o proíbe a remoção da licença. Se um utilizador tiver uma licença que está dependente de uma licença que está a ser removida devido a eliminação do grupo, a atribuição de licenças para o utilizador é convertida de herdadas para direcionar o.

Por exemplo, considere um grupo que tenha o Office 365 E3/E5 atribuída com um Skype para o plano do serviço de negócios ativado. Imagine também que alguns membros do grupo têm licenças de conferência de áudio atribuídas diretamente. Quando o grupo é eliminado, o licenciamento baseado em grupo tentará remover o Office 365 E3/E5 de todos os utilizadores. Como conferência de áudio é dependente do Skype para empresas, para os utilizadores que tenham conferência de áudio atribuído, com base em grupo licenciamento converte as licenças do Office 365 E3/E5 para direcionar a atribuição de licenças.

## <a name="how-do-you-manage-licenses-for-products-with-prerequisites"></a>Como gerir licenças de produtos com os pré-requisitos?

Alguns produtos Online da Microsoft que pode possuir são *suplementos*. Suplementos requerem um plano do serviço de pré-requisitos para ser ativada para um utilizador ou um grupo antes de pode ser atribuídas uma licença. Com o licenciamento baseado em grupo, o sistema exige que o pré-requisito e o suplemento planos do serviço estar presentes no mesmo grupo. Isso é feito para garantir que qualquer usuário que é adicionado ao grupo pode receber o produto de trabalho totalmente. Vejamos o exemplo seguinte:

Microsoft Workplace Analytics é um produto de complemento. Ele contém um plano de serviço único com o mesmo nome. Podemos apenas atribuir este plano do serviço para um utilizador ou grupo, quando um dos seguintes pré-requisitos também está atribuído:
- Exchange Online (plano 1) 
- Exchange Online (plano 2)

Se vamos tentar atribuir neste produto em seu próprio a um grupo, o portal devolve um erro. Selecionar a notificação de erro mostra os seguintes detalhes:

![Grupo, pré-requisitos em falta](./media/licensing-groups-resolve-problems/group-prerequisite-required.png)

Se selecionarmos os detalhes, mostra a mensagem de erro seguinte:

>Falha na operação de licença. Certifique-se de que o grupo tem serviços necessários antes de adicionar ou remover um serviço dependente. **O serviço Microsoft Workplace Analytics requer Exchange Online (plano 2) para seja também ativado.**

Para atribuir esta licença de suplemento a um grupo, podemos tem de garantir que o grupo contém também o plano do serviço de pré-requisitos. Por exemplo, podemos poderá atualizar um grupo existente que já contém o produto completo do Office 365 E3 e, em seguida, adicionar o produto de complemento ao mesmo.

Também é possível criar um grupo de autónomo que contém apenas os produtos mínimos necessários para tornar o suplemento funcione. Pode ser utilizado para licenciar os utilizadores selecionados para o produto de complemento. Neste exemplo, atribuído os seguintes produtos para o mesmo grupo:
- Office 365 Enterprise E3, com apenas o plano do serviço Exchange Online (plano 2) ativado
- Microsoft Workplace Analytics

![Grupo, pré-requisito incluído](./media/licensing-groups-resolve-problems/group-addon-with-prerequisite.png)

De agora em diante, quaisquer utilizadores adicionados a este grupo consumam uma licença do produto E3 e uma licença do produto de análise de área de trabalho. Ao mesmo tempo, esses utilizadores podem ser membros de outro grupo que dá a eles o produto completo de E3 e continuam a consumir somente uma licença do produto em questão.

> [!TIP]
> Pode criar vários grupos para cada plano do serviço de pré-requisitos. Por exemplo, se utilizar o Office 365 Enterprise E1 e o Office 365 Enterprise E3 para os seus utilizadores, pode criar dois grupos a licença Microsoft Workplace Analytics: um que utiliza E1 como um pré-requisito e outra que utiliza E3. Isso permite que distribuir o complemento para os utilizadores E1 e E3 sem consumir licenças adicionais.

## <a name="how-do-you-force-license-processing-in-a-group-to-resolve-errors"></a>Como forçar o processamento de licença num grupo para resolver erros?

Dependendo de quais etapas que seguimos para resolver os erros, poderá ser necessário acionar manualmente o processamento de um grupo para atualizar o estado do utilizador.

Por exemplo, se libertar algumas licenças ao remover atribuições diretas de licenças de utilizadores, terá de acionar o processamento de grupos que tenha falhado anteriormente totalmente licenciar todos os membros de utilizador. Reprocessar um grupo, vá para o painel de grupo, abra **licenças**e, em seguida, selecione a **Reprocessar** botão na barra de ferramentas.

## <a name="how-do-you-force-license-processing-on-a-user-to-resolve-errors"></a>Como forçar o processamento de licença num utilizador para resolver erros?

Dependendo de quais etapas que seguimos para resolver os erros, poderá ser necessário acionar manualmente o processamento de um utilizador para atualizar o estado de usuários.

Por exemplo, depois de resolver o problema de endereço de proxy duplicados para um utilizador afetado, precisa acionar o processamento do utilizador. Reprocessar um utilizador, vá para o painel do utilizador, abra **licenças**e, em seguida, selecione a **Reprocessar** botão na barra de ferramentas.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre outros cenários para gestão de licenças através de grupos, consulte o seguinte:

* [O que é o licenciamento no Azure Active Directory com base em grupo?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
* [Atribuir licenças a um grupo no Azure Active Directory](licensing-groups-assign.md)
* [Como migrar os utilizadores licenciados individuais para o licenciamento baseado no grupo no Azure Active Directory](licensing-groups-migrate-users.md)
* [Como migrar os utilizadores entre licenças de produto através do licenciamento com o botão com base em grupo no Azure Active Directory](licensing-groups-change-licenses.md)
* [Cenários adicionais de licenciamento baseado no grupo do Azure Active Directory](licensing-group-advanced.md)
* [Exemplos do PowerShell para licenciamento com o botão com base em grupo no Azure Active Directory](licensing-ps-examples.md)
