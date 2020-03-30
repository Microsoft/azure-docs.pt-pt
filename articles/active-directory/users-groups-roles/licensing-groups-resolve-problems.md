---
title: Resolver problemas de atribuição de licenças de grupo - Diretório Ativo Azure / Microsoft Docs
description: Como identificar e resolver problemas de atribuição de licenças quando estiver a utilizar o licenciamento baseado em grupo azure Ative Directory
services: active-directory
keywords: Licenciamento do Azure AD
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ddfc4bf7ed3bdf214a44a5dfe03259d32b2f3f94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74025702"
---
# <a name="identify-and-resolve-license-assignment-problems-for-a-group-in-azure-active-directory"></a>Identificar e resolver problemas de atribuição de licenças para um grupo no Diretório Ativo do Azure

O licenciamento baseado em grupo no Azure Ative Directory (Azure AD) introduz o conceito de utilizadores num estado de erro de licenciamento. Neste artigo, explicamos as razões pelas quais os utilizadores podem acabar neste estado.

Ao atribuir licenças diretamente a utilizadores individuais, sem utilizar o licenciamento baseado em grupo, a operação de atribuição poderá falhar. Por exemplo, quando executa o `Set-MsolUserLicense` cmdlet PowerShell num sistema de utilizador, o cmdlet pode falhar por muitas razões relacionadas com a lógica empresarial. Por exemplo, pode haver um número insuficiente de licenças ou um conflito entre dois planos de serviço que não podem ser atribuídos ao mesmo tempo. O problema é imediatamente comunicado.

Quando se está a utilizar o licenciamento baseado em grupo, podem ocorrer os mesmos erros, mas acontecem em segundo plano enquanto o serviço Azure AD está a atribuir licenças. Por esta razão, os erros não podem ser comunicados imediatamente. Em vez disso, são gravados no objeto do utilizador e depois reportados através do portal administrativo. A intenção original de licenciar o utilizador nunca se perde, mas está registada num estado de erro para futura investigação e resolução.

## <a name="find-license-assignment-errors"></a>Encontre erros de atribuição de licenças

### <a name="to-find-users-in-an-error-state-in-a-group"></a>Para encontrar utilizadores em um estado de erro em um grupo

1. Abra o grupo na sua página geral e selecione **Licenças**. Uma notificação aparece se houver algum utilizador em estado de erro.

   ![Mensagem de notificações de grupo e erro](./media/licensing-groups-resolve-problems/group-error-notification.png)

1. Selecione a notificação para abrir uma lista de todos os utilizadores afetados. Pode selecionar cada utilizador individualmente para ver mais detalhes.

   ![lista de utilizadores no estado de erro de licenciamento de grupo](./media/licensing-groups-resolve-problems/list-of-users-with-errors.png)

1. Para encontrar todos os grupos que contenham pelo menos um erro, na lâmina de **diretório Ativo Azure** selecione **Licenças,** e, em seguida, selecione **Visão Geral**. É apresentada uma caixa de informação quando os grupos requerem a sua atenção.

   ![Visão geral e informação sobre grupos em estado de erro](./media/licensing-groups-resolve-problems/group-errors-widget.png)

1. Selecione a caixa para ver uma lista de todos os grupos com erros. Pode selecionar cada grupo para mais detalhes.

   ![Visão geral e lista de grupos com erros](./media/licensing-groups-resolve-problems/list-of-groups-with-errors.png)

As seguintes secções dão uma descrição de cada problema potencial e a forma de o resolver.

## <a name="not-enough-licenses"></a>Não há licenças suficientes.

**Problema:** Não há licenças disponíveis suficientes para um dos produtos especificados no grupo. Precisa de adquirir mais licenças para o produto ou libertar licenças não utilizadas de outros utilizadores ou grupos.

Para ver quantas licenças estão disponíveis, vá a > **Licenças** > de **Diretório Ativo Azure****Todos os produtos.**

Para ver quais os utilizadores e grupos que estão a consumir licenças, selecione um produto. Nos **utilizadores Licenciados,** vê-se uma lista de todos os utilizadores que tenham tido licenças atribuídas diretamente ou através de um ou mais grupos. Sob **grupos licenciados,** você vê todos os grupos que têm esses produtos atribuídos.

**PowerShell:** Os cmdlets PowerShell reportam este erro como _CountViolation_.

## <a name="conflicting-service-plans"></a>Planos de serviço sinuosos

**Problema:** Um dos produtos especificados no grupo contém um plano de serviço que entra em conflito com outro plano de serviço que já está atribuído ao utilizador através de um produto diferente. Alguns planos de serviço são configurados de uma forma que não podem ser atribuídos ao mesmo utilizador que outro plano de serviço relacionado.

Considere o seguinte exemplo. Um utilizador tem uma licença para o Office 365 Enterprise *E1* atribuído diretamente, com todos os planos ativados. O utilizador foi adicionado a um grupo que tem o produto Office 365 Enterprise *E3* atribuído ao mesmo. O produto E3 contém planos de serviço que não podem sobrepor-se aos planos incluídos na E1, pelo que a atribuição de licença de grupo falha com o erro dos "planos de serviço contraditórios". Neste exemplo, os planos de serviços contraditórios são:

- O SharePoint Online (Plano 2) entra em conflito com o SharePoint Online (Plano 1).
- Troca online (Plano 2) entra em conflito com o Exchange Online (Plano 1).

Para resolver este conflito, é preciso desativar dois dos planos. Pode desativar a licença E1 que é diretamente atribuída ao utilizador. Ou, você precisa modificar toda a atribuição de licença de grupo e desativar os planos na licença E3. Em alternativa, poderá decidir remover a licença E1 do utilizador se for redundante no contexto da licença E3.

A decisão sobre como resolver licenças de produtos conflituosas pertence sempre ao administrador. A AD Azure não resolve automaticamente os conflitos de licenças.

**PowerShell:** Os cmdlets PowerShell reportam este erro como _MutualexclusiveViolation_.

## <a name="other-products-depend-on-this-license"></a>Outros produtos dependem desta licença

**Problema:** Um dos produtos especificados no grupo contém um plano de serviço que deve ser ativado para que outro plano de serviço, noutro produto, funcione. Este erro ocorre quando a AD Azure tenta remover o plano de serviço subjacente. Por exemplo, isto pode acontecer quando se retira o utilizador do grupo.

Para resolver este problema, é necessário certificar-se de que o plano exigido ainda é atribuído aos utilizadores através de outro método ou que os serviços dependentes são desativados para esses utilizadores. Depois disso, pode remover corretamente a licença de grupo desses utilizadores.

**PowerShell:** Os cmdlets PowerShell reportam este erro como _Violação da Dependência_.

## <a name="usage-location-isnt-allowed"></a>A localização de uso não é permitida

**Problema:** Alguns serviços da Microsoft não estão disponíveis em todos os locais devido às leis e regulamentos locais. Antes de poder atribuir uma licença a um utilizador, deve especificar a propriedade de **localização de utilização** para o utilizador. Pode especificar a localização na secção**Definições** de**Perfil** > do **Utilizador** > no portal Azure.

Quando a Azure AD tenta atribuir uma licença de grupo a um utilizador cuja localização de utilização não é suportada, falha e regista um erro no utilizador.

Para resolver este problema, remova os utilizadores de locais não suportados do grupo licenciado. Alternativamente, se os valores de localização de utilização atuais não representarem a localização real do utilizador, pode modificá-los de modo a que as licenças sejam corretamente atribuídas da próxima vez (se a nova localização for suportada).

**PowerShell:** Os cmdlets PowerShell reportam este erro como _Proibição De Utilização DeUsoViolação_.

> [!NOTE]
> Quando a Azure AD atribui licenças de grupo, quaisquer utilizadores sem uma localização de utilização especificada herdam a localização do diretório. Recomendamos que os administradores estabeleçam os valores de localização de utilização corretos nos utilizadores antes de utilizarem o licenciamento baseado em grupo para cumprir as leis e regulamentos locais.

## <a name="duplicate-proxy-addresses"></a>Endereços de procuração duplicados

Se utilizar o Exchange Online, alguns utilizadores do seu inquilino podem estar incorretamente configurados com o mesmo valor de endereço de procuração. Quando o licenciamento baseado em grupo tenta atribuir uma licença a esse utilizador, falha e mostra que "o endereço Proxy já está a ser utilizado".

> [!TIP]
> Para ver se existe um endereço de procuração duplicado, execute o seguinte cmdlet PowerShell contra exchange Online:
> ```
> Get-Recipient -ResultSize unlimited | where {$_.EmailAddresses -match "user@contoso.onmicrosoft.com"} | fL Name, RecipientType,emailaddresses
> ```
> Para obter mais informações sobre este problema, consulte a mensagem de [erro "Proxy address already being used" no Exchange Online](https://support.microsoft.com/help/3042584/-proxy-address-address-is-already-being-used-error-message-in-exchange-online). O artigo também inclui informações sobre [como ligar-se ao Exchange Online utilizando powerShell remoto.](https://technet.microsoft.com/library/jj984289.aspx)

Depois de resolver quaisquer problemas de endereço de procuração para os utilizadores afetados, certifique-se de forçar o processamento da licença no grupo para se certificar de que as licenças podem agora ser aplicadas.

## <a name="azure-ad-mail-and-proxyaddresses-attribute-change"></a>Azure AD Mail e ProxyAddresss atribuem mudança

**Problema:** Ao atualizar a atribuição de licença sumição de um utilizador ou de um grupo, pode ver que o atributo de Correio AD Azure e ProxyAddresss de alguns utilizadores são alterados.

Atualizar a atribuição de licença num utilizador faz com que o cálculo do endereço proxy seja desencadeado, o que pode alterar os atributos do utilizador. Para compreender a razão exata da mudança e resolver o problema, consulte este artigo sobre [como o atributo proxyAddresss é povoado em Azure AD](https://support.microsoft.com/help/3190357/how-the-proxyaddresses-attribute-is-populated-in-azure-ad).

## <a name="licenseassignmentattributeconcurrencyexception-in-audit-logs"></a>Atribuição de LicençaSAtribuiçãoConcurrencyExcep em registos de auditoria

**Problema:** O utilizador tem LicençaAssignmentAttributeConcurrencyException para atribuição de licença em registos de auditoria.
Quando o licenciamento baseado em grupo tenta processar a atribuição simultânea de licença da mesma licença a um utilizador, esta exceção é registada no utilizador. Isto geralmente acontece quando um utilizador é membro de mais de um grupo com a mesma licença atribuída. A AZure AD irá voltar a processar a licença de utilizador e resolverá o problema. Não é necessária nenhuma ação do cliente para corrigir este problema.

## <a name="more-than-one-product-license-assigned-to-a-group"></a>Mais de uma licença de produto atribuída a um grupo

Pode atribuir mais de uma licença de produto a um grupo. Por exemplo, pode atribuir o Office 365 Enterprise E3 e a Enterprise Mobility + Security a um grupo para permitir facilmente todos os serviços incluídos para os utilizadores.

A Azure AD tenta atribuir todas as licenças especificadas no grupo a cada utilizador. Se a Azure AD não conseguir atribuir um dos produtos devido a problemas de lógica empresarial, também não atribuirá as outras licenças do grupo. Um exemplo é se não há licenças suficientes para todos, ou se existem conflitos com outros serviços que estão ativados no utilizador.

Pode ver os utilizadores que não conseguiram ser atribuídos e verificar quais os produtos afetados por este problema.

## <a name="when-a-licensed-group-is-deleted"></a>Quando um grupo licenciado é eliminado

Deve remover todas as licenças atribuídas a um grupo antes de poder eliminar o grupo. No entanto, a remoção das licenças de todos os utilizadores do grupo pode demorar algum tempo. Ao remover as atribuições de licença de um grupo, pode haver falhas se o utilizador tiver uma licença dependente atribuída ou se houver um problema de conflito de endereços proxy que proíbe a remoção da licença. Se um utilizador tiver uma licença dependente de uma licença que está a ser removida devido à eliminação do grupo, a atribuição de licença ao utilizador é convertida de herdada para direta.

Por exemplo, considere um grupo que tenha o Office 365 E3/E5 atribuído com um plano de serviço Skype para negócios habilitado. Também imagine que alguns membros do grupo têm licenças de Conferência de Áudio atribuídas diretamente. Quando o grupo é eliminado, o licenciamento baseado em grupo tentará remover o Office 365 E3/E5 de todos os utilizadores. Uma vez que a Conferência de Áudio está dependente do Skype for Business, para quaisquer utilizadores com A Conferência de Áudio atribuído, o licenciamento baseado em grupo converte as licenças Do Office 365 E3/E5 para atribuição direta de licença.

## <a name="manage-licenses-for-products-with-prerequisites"></a>Gerir licenças para produtos com pré-requisitos

Alguns produtos Microsoft Online que pode possuir são *add-ons*. Os add-ons requerem um plano de serviço pré-requisito para ser ativado para um utilizador ou um grupo antes de poderem ser atribuídos uma licença. Com o licenciamento baseado em grupo, o sistema requer que tanto os planos de serviço pré-requisito como os planos de atendimento adicionais estejam presentes no mesmo grupo. Isto é feito para garantir que todos os utilizadores adicionados ao grupo possam receber o produto totalmente em funcionamento. Considere o seguinte exemplo:

O Microsoft Workplace Analytics é um produto adicionais. Contém um único plano de serviço com o mesmo nome. Só podemos atribuir este plano de serviço a um utilizador, ou grupo, quando um dos seguintes pré-requisitos também for atribuído:

- Troca online (Plano 1)
- Troca online (Plano 2)

Se tentarmos atribuir este produto por si só a um grupo, o portal devolve uma mensagem de notificação. Se selecionarmos os detalhes do item, mostra a seguinte mensagem de erro:

  "A operação de licença falhou. Certifique-se de que o grupo dispõe de serviços necessários antes de adicionar ou remover um serviço dependente. O serviço Microsoft Workplace Analytics exige que o **Exchange Online (Plano 2) também seja ativado.**

Para atribuir esta licença de complemento a um grupo, temos de garantir que o grupo também contém o plano de serviço pré-requisito. Por exemplo, podemos atualizar um grupo existente que já contém o produto 365 E3 completo e, em seguida, adicionar-lhe o produto add-on.

Também é possível criar um grupo autónomo que contenha apenas os produtos mínimos necessários para fazer o trabalho de complemento. Pode ser usado para licenciar apenas utilizadores selecionados para o produto add-on. Com base no exemplo anterior, atribuiria os seguintes produtos ao mesmo grupo:

- Office 365 Enterprise E3 com apenas o plano de serviço Exchange Online (Plano 2) habilitado
- Análise de Área de Trabalho da Microsoft

A partir de agora, todos os utilizadores adicionados a este grupo consomem uma licença do produto E3 e uma licença do produto Workplace Analytics. Ao mesmo tempo, estes utilizadores podem ser membros de outro grupo que lhes dá todo o produto E3, e ainda consomem apenas uma licença para esse produto.

> [!TIP]
> Pode criar vários grupos para cada plano de serviço pré-requisito. Por exemplo, se utilizar tanto o Office 365 Enterprise E1 como o Office 365 Enterprise E3 para os seus utilizadores, pode criar dois grupos para licenciar o Microsoft Workplace Analytics: um que utiliza a E1 como pré-requisito e o outro que utiliza o E3. Isto permite-lhe distribuir o complemento aos utilizadores da E1 e E3 sem consumir licenças adicionais.

## <a name="force-group-license-processing-to-resolve-errors"></a>Processamento de licença de grupo de força para resolver erros

Dependendo das medidas que tomou para resolver os erros, pode ser necessário desencadear manualmente o processamento de um grupo para atualizar o estado do utilizador.

Por exemplo, se libertar algumas licenças removendo as atribuições de licenças diretas dos utilizadores, precisa de desencadear o processamento de grupos que anteriormente não licenciaram totalmente todos os utilizadores. Para reprocessar um grupo, vá ao painel de grupo, abra **licenças**e, em seguida, selecione o botão **Reprocessar** na barra de ferramentas.

## <a name="force-user-license-processing-to-resolve-errors"></a>Forçar o processamento de licença de utilizador para resolver erros

Dependendo das medidas que tomou para resolver os erros, pode ser necessário desencadear manualmente o processamento de um utilizador para atualizar o estado dos utilizadores.

Por exemplo, depois de resolver o problema de endereço de procuração duplicado para um utilizador afetado, tem de desencadear o processamento do utilizador. Para reprocessar um utilizador, vá ao painel do utilizador, abra **licenças**e, em seguida, selecione o botão **Reprocessar** na barra de ferramentas.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre outros cenários para a gestão de licenças através de grupos, consulte o seguinte:

* [O que é o licenciamento baseado em grupo no Azure Ative Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
* [Atribuir licenças a um grupo no Azure Active Directory](licensing-groups-assign.md)
* [Como migrar os utilizadores licenciados individuais para o licenciamento baseado no grupo no Azure Active Directory](licensing-groups-migrate-users.md)
* [Como migrar os utilizadores entre licenças de produtos utilizando licenciamento baseado em grupo no Diretório Ativo azure](licensing-groups-change-licenses.md)
* [Cenários adicionais de licenciamento baseado no grupo do Azure Active Directory](licensing-group-advanced.md)
* [Exemplos da PowerShell para licenciamento baseado em grupo no Diretório Ativo Azure](licensing-ps-examples.md)
