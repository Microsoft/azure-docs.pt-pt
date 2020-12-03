---
title: Resolver problemas de atribuição de licenças de grupo - Azure Ative Directory / Microsoft Docs
description: Como identificar e resolver problemas de atribuição de licenças quando estiver a utilizar o licenciamento baseado no grupo Azure Ative Directory
services: active-directory
keywords: Licenciamento do Azure AD
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: how-to
ms.workload: identity
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc3dce8bb51299eb78bd2958eebf7a69594318a4
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96546476"
---
# <a name="identify-and-resolve-license-assignment-problems-for-a-group-in-azure-active-directory"></a>Identificar e resolver problemas de atribuição de licenças para um grupo no Azure Ative Directory

O licenciamento baseado em grupo no Azure Ative Directory (Azure AD) introduz o conceito de utilizadores num estado de erro de licenciamento. Neste artigo, explicamos as razões pelas quais os utilizadores podem acabar neste estado.

Quando atribui licenças diretamente a utilizadores individuais, sem utilizar o licenciamento baseado em grupo, a operação de atribuição poderá falhar. Por exemplo, quando executa o cmdlet PowerShell `Set-MsolUserLicense` num sistema de utilizador, o cmdlet pode falhar por muitas razões relacionadas com a lógica do negócio. Por exemplo, pode haver um número insuficiente de licenças ou um conflito entre dois planos de serviço que não podem ser atribuídos ao mesmo tempo. O problema é imediatamente comunicado.

Quando está a usar o licenciamento baseado em grupo, os mesmos erros podem ocorrer, mas acontecem em segundo plano enquanto o serviço AD AZure está a atribuir licenças. Por esta razão, os erros não podem ser comunicados imediatamente. Em vez disso, são gravados no objeto do utilizador e depois reportados através do portal administrativo. A intenção original de licenciar o utilizador nunca é perdida, mas é registada num estado de erro para futura investigação e resolução.

## <a name="find-license-assignment-errors"></a>Encontrar erros de atribuição de licenças

### <a name="to-find-users-in-an-error-state-in-a-group"></a>Encontrar utilizadores num estado de erro num grupo

1. Abra o grupo para a sua página de visão geral e selecione **Licenças**. Uma notificação aparece se houver algum utilizadores em estado de erro.

   ![Mensagem de notificações de grupo e erro](./media/licensing-groups-resolve-problems/group-error-notification.png)

1. Selecione a notificação para abrir uma lista de todos os utilizadores afetados. Pode selecionar cada utilizador individualmente para ver mais detalhes.

   ![lista de utilizadores em estado de erro de licenciamento de grupo](./media/licensing-groups-resolve-problems/list-of-users-with-errors.png)

1. Para encontrar todos os grupos que contenham pelo menos um erro, na lâmina do **Diretório Ativo Azure** selecione **Licenças** e, em seguida, selecione **Overview**. Uma caixa de informações é exibida quando os grupos requerem a sua atenção.

   ![Visão geral e informação sobre grupos em estado de erro](./media/licensing-groups-resolve-problems/group-errors-widget.png)

1. Selecione a caixa para ver uma lista de todos os grupos com erros. Pode selecionar cada grupo para mais detalhes.

   ![Visão geral e lista de grupos com erros](./media/licensing-groups-resolve-problems/list-of-groups-with-errors.png)

As secções seguintes dão uma descrição de cada problema potencial e a forma de o resolver.

## <a name="not-enough-licenses"></a>Não há licenças suficientes.

**Problema:** Não há licenças disponíveis suficientes para um dos produtos especificados no grupo. Você precisa comprar mais licenças para o produto ou libertar licenças não-usadas de outros utilizadores ou grupos.

Para ver quantas licenças estão disponíveis, aceda a **Azure Ative Directory**  >  **Licenses**  >  **Todos os produtos.**

Para ver quais utilizadores e grupos estão a consumir licenças, selecione um produto. Nos **utilizadores licenciados,** vê uma lista de todos os utilizadores que tenham licenças atribuídas diretamente ou através de um ou mais grupos. Em **grupos licenciados,** você vê todos os grupos que têm os produtos atribuídos.

**PowerShell:** Os cmdlets PowerShell reportam este erro como _CondeViolation_.

## <a name="conflicting-service-plans"></a>Planos de serviço contraditórios

**Problema:** Um dos produtos especificados no grupo contém um plano de serviço que entra em conflito com outro plano de serviço que já foi atribuído ao utilizador através de um produto diferente. Alguns planos de serviço são configurados de uma forma que não podem ser atribuídos ao mesmo utilizador que outro plano de serviço relacionado.

Considere o seguinte exemplo. Um utilizador tem uma licença para o Office 365 Enterprise *E1* atribuído diretamente, com todos os planos habilitados. O utilizador foi adicionado a um grupo que tem o produto *E3* do Office 365 Enterprise atribuído ao mesmo. O produto E3 contém planos de serviço que não se podem sobrepor aos planos incluídos na E1, pelo que a atribuição da licença de grupo falha com o erro dos "planos de serviço conflituosos". Neste exemplo, os planos de serviço contraditórios são:

- SharePoint Online (Plano 2) entra em conflito com o SharePoint Online (Plano 1).
- Exchange Online (Plano 2) entra em conflito com o Exchange Online (Plano 1).

Para resolver este conflito, é preciso desativar dois dos planos. Pode desativar a licença E1 que está diretamente atribuída ao utilizador. Ou, você precisa modificar toda a atribuição de licença de grupo e desativar os planos na licença E3. Em alternativa, poderá decidir remover a licença E1 do utilizador se for redundante no contexto da licença E3.

A decisão sobre como resolver licenças de produtos conflituosas pertence sempre ao administrador. A Azure AD não resolve automaticamente conflitos de licenças.

**PowerShell:** Os cmdlets powerShell reportam este erro como _MutuallyExclusiveViolation_.

## <a name="other-products-depend-on-this-license"></a>Outros produtos dependem desta licença

**Problema:** Um dos produtos especificados no grupo contém um plano de serviço que deve ser habilitado para que outro plano de serviço, em outro produto, funcione. Este erro ocorre quando a Azure AD tenta remover o plano de serviço subjacente. Por exemplo, isto pode acontecer quando retirar o utilizador do grupo.

Para resolver este problema, é necessário certificar-se de que o plano necessário ainda é atribuído aos utilizadores através de outro método ou de que os serviços dependentes são desativados para esses utilizadores. Depois de o fazer, pode remover corretamente a licença de grupo desses utilizadores.

**PowerShell:** Os cmdlets PowerShell reportam este erro como _A Violência de Dependência_.

## <a name="usage-location-isnt-allowed"></a>Localização de utilização não é permitida

**Problema:** Alguns serviços da Microsoft não estão disponíveis em todos os locais devido às leis e regulamentos locais. Antes de poder atribuir uma licença a um utilizador, tem de especificar a propriedade **de localização de Utilização** para o utilizador. Pode especificar a localização na secção De edição de perfil do **utilizador**  >  **Profile**  >  **Edit** no portal Azure.

Quando a Azure AD tenta atribuir uma licença de grupo a um utilizador cuja localização de utilização não é suportada, falha e regista um erro no utilizador.

Para resolver este problema, remova os utilizadores de locais não suportados do grupo licenciado. Em alternativa, se os valores atuais de localização de utilização não representarem a localização real do utilizador, pode modificá-las de modo a que as licenças sejam corretamente atribuídas da próxima vez (se a nova localização for suportada).

**PowerShell:** Os cmdlets PowerShell reportam este erro como _ProibidoInUsageLocationViolation_.

> [!NOTE]
> Quando a Azure AD atribui licenças de grupo, qualquer utilizadores sem uma localização de utilização especificada herda a localização do diretório. Recomendamos que os administradores estabeleçam os valores corretos de localização de utilização nos utilizadores antes de utilizarem o licenciamento baseado no grupo para cumprirem as leis e regulamentos locais.

## <a name="duplicate-proxy-addresses"></a>Endereços de procuração duplicados

Se utilizar o Exchange Online, alguns utilizadores da sua organização poderão estar configurados incorretamente com o mesmo valor de endereço proxy. Quando o licenciamento baseado em grupo tenta atribuir uma licença a esse utilizador, este falha e mostra que "o endereço Proxy já está a ser usado".

> [!TIP]
> Para ver se existe um endereço de procuração duplicado, execute o seguinte cmdlet PowerShell contra Exchange Online:
> ```
> Get-Recipient -ResultSize unlimited | where {$_.EmailAddresses -match "user@contoso.onmicrosoft.com"} | fL Name, RecipientType,emailaddresses
> ```
> Para obter mais informações sobre este problema, consulte [a mensagem de erro "Proxy já está a ser utilizada" no Exchange Online](https://support.microsoft.com/help/3042584/-proxy-address-address-is-already-being-used-error-message-in-exchange-online). O artigo também inclui informações sobre [como ligar-se ao Exchange Online utilizando o PowerShell remoto.](/powershell/exchange/connect-to-exchange-online-powershell?view=exchange-ps)

Depois de resolver quaisquer problemas de procuração para os utilizadores afetados, certifique-se de forçar o processamento de licenças no grupo para se certificar de que as licenças podem agora ser aplicadas.

## <a name="azure-ad-mail-and-proxyaddresses-attribute-change"></a>Azure AD Mail e ProxyAddresses atribuem alteração de atributos

**Problema:** Ao atualizar a atribuição de licenças num utilizador ou grupo, poderá ver que o atributo AD Mail e ProxyAddresses do Azure São alterados.

Atualizar a atribuição de licença num utilizador faz com que o cálculo do endereço proxy seja acionado, o que pode alterar os atributos do utilizador. Para compreender a razão exata da mudança e resolver o problema, consulte este artigo sobre [como o atributo proxyAddresses é preenchido em Azure AD](https://support.microsoft.com/help/3190357/how-the-proxyaddresses-attribute-is-populated-in-azure-ad).

## <a name="licenseassignmentattributeconcurrencyexception-in-audit-logs"></a>LicençaAssignmentAttributeConcurrencyExcepção em registos de auditoria

**Problema:** O utilizador tem LicençaAssignmentAttributeConcurrencyExcepção para atribuição de licença em registos de auditoria.
Quando o licenciamento baseado em grupo tenta processar a atribuição simultânea de licença da mesma licença a um utilizador, esta exceção é registada no utilizador. Isto geralmente acontece quando um utilizador é membro de mais de um grupo com a mesma licença atribuída. O Azure AD tentará novamente processar a licença do utilizador e resolverá o problema. Não é necessária nenhuma ação por parte do cliente para corrigir este problema.

## <a name="more-than-one-product-license-assigned-to-a-group"></a>Mais de uma licença de produto atribuída a um grupo

Pode atribuir mais do que uma licença de produto a um grupo. Por exemplo, pode atribuir o Office 365 Enterprise E3 e a Enterprise Mobility + Security a um grupo para permitir facilmente todos os serviços incluídos para os utilizadores.

A Azure AD tenta atribuir todas as licenças especificadas no grupo a cada utilizador. Se a Azure AD não puder atribuir um dos produtos devido a problemas de lógica comercial, também não atribuirá as outras licenças do grupo. Um exemplo é se não houver licenças suficientes para todos, ou se houver conflitos com outros serviços que estejam habilitados no utilizador.

Pode ver os utilizadores que não foram designados e verificar quais os produtos que são afetados por este problema.

## <a name="when-a-licensed-group-is-deleted"></a>Quando um grupo licenciado é eliminado

Deve remover todas as licenças atribuídas a um grupo antes de poder eliminar o grupo. No entanto, a remoção de licenças de todos os utilizadores do grupo pode demorar algum tempo. Ao remover as atribuições de licença de um grupo, pode haver falhas se o utilizador tiver uma licença dependente atribuída ou se houver um problema de conflito de endereço de procuração que proíba a remoção da licença. Se um utilizador tiver uma licença que esteja dependente de uma licença que está a ser removida devido à eliminação do grupo, a atribuição da licença ao utilizador é convertida de herdada para direta.

Por exemplo, considere um grupo que tenha o Office 365 E3/E5 atribuído com um plano de serviço Skype para negócios habilitado. Imagine também que alguns membros do grupo têm licenças de AudioConferência atribuídas diretamente. Quando o grupo for eliminado, o licenciamento baseado em grupo tentará remover o Office 365 E3/E5 de todos os utilizadores. Como a Audio Conferencing depende do Skype para negócios, para qualquer utilizadores com Audio Conferencing atribuído, o licenciamento baseado em grupo converte as licenças do Office 365 E3/E5 para a atribuição de licenças diretas.

## <a name="manage-licenses-for-products-with-prerequisites"></a>Gerir licenças para produtos com pré-requisitos

Alguns produtos Microsoft Online que pode possuir são *add-ons*. Os addons requerem um plano de serviço pré-requisito para ser ativado para um utilizador ou um grupo antes de poderem ser atribuídos uma licença. Com o licenciamento baseado em grupo, o sistema exige que os planos de serviço pré-requisito e complemento estejam presentes no mesmo grupo. Isto é feito para garantir que todos os utilizadores que são adicionados ao grupo podem receber o produto totalmente funcionando. Considere o seguinte exemplo:

O Microsoft Workplace Analytics é um produto adicional. Contém um único plano de serviço com o mesmo nome. Só podemos atribuir este plano de serviço a um utilizador, ou grupo, quando um dos seguintes pré-requisitos também é atribuído:

- Intercâmbio Online (Plano 1)
- Intercâmbio Online (Plano 2)

Se tentarmos atribuir este produto por si só a um grupo, o portal devolve uma mensagem de notificação. Se selecionarmos os detalhes do item, ele mostra a seguinte mensagem de erro:

  "A operação de licença falhou. Certifique-se de que o grupo dispõe dos serviços necessários antes de adicionar ou remover um serviço dependente. **O serviço Microsoft Workplace Analytics requer que o Exchange Online (Plano 2) também seja ativado.**

Para atribuir esta licença de complemento a um grupo, temos de garantir que o grupo também contém o plano de serviço pré-requisito. Por exemplo, podemos atualizar um grupo existente que já contém o produto Completo Office 365 E3 e, em seguida, adicionar-lhe o produto addon.

Também é possível criar um grupo autónomo que contenha apenas os produtos mínimos necessários para fazer o complemento funcionar. Pode ser usado para licenciar apenas utilizadores selecionados para o produto add-on. Com base no exemplo anterior, atribuiria os seguintes produtos ao mesmo grupo:

- Office 365 Enterprise E3 com apenas o plano de serviço Exchange Online (Plano 2) habilitado
- Análise de Área de Trabalho da Microsoft

A partir de agora, qualquer utilizadores adicionados a este grupo consomem uma licença do produto E3 e uma licença do produto Workplace Analytics. Ao mesmo tempo, esses utilizadores podem ser membros de outro grupo que lhes dá o produto E3 completo, e ainda consomem apenas uma licença para esse produto.

> [!TIP]
> Pode criar vários grupos para cada plano de serviço pré-requisito. Por exemplo, se utilizar tanto o Office 365 Enterprise E1 como o Office 365 Enterprise E3 para os seus utilizadores, pode criar dois grupos para licenciar o Microsoft Workplace Analytics: um que utiliza o E1 como pré-requisito e o outro que utiliza o E3. Isto permite-lhe distribuir o complemento aos utilizadores E1 e E3 sem consumir licenças adicionais.

## <a name="force-group-license-processing-to-resolve-errors"></a>Forçar o processamento de licenças de grupo para resolver erros

Dependendo dos passos que tomou para resolver os erros, pode ser necessário acionar manualmente o processamento de um grupo para atualizar o estado do utilizador.

Por exemplo, se libertar algumas licenças removendo as atribuições de licenças diretas dos utilizadores, precisa de desencadear o processamento de grupos que anteriormente não licenciaram totalmente todos os membros do utilizador. Para reprocessar um grupo, vá ao painel de grupo, abra **licenças** e, em seguida, selecione o botão **reprocessamento** na barra de ferramentas.

## <a name="force-user-license-processing-to-resolve-errors"></a>Force o processamento da licença de utilizador para resolver erros

Dependendo dos passos que tomou para resolver os erros, pode ser necessário acionar manualmente o processamento de um utilizador para atualizar o estado dos utilizadores.

Por exemplo, depois de resolver o problema de endereço de procuração duplicado para um utilizador afetado, tem de ativar o processamento do utilizador. Para reprocessar um utilizador, vá ao painel de utilizador, abra **licenças** e, em seguida, selecione o botão **reprocessamento** na barra de ferramentas.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre outros cenários para a gestão de licenças através de grupos, consulte o seguinte:

* [O que é o licenciamento baseado em grupo no Azure Ative Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
* [Atribuir licenças a um grupo no Azure Active Directory](./licensing-groups-assign.md)
* [Como migrar os utilizadores licenciados individuais para o licenciamento baseado no grupo no Azure Active Directory](licensing-groups-migrate-users.md)
* [Como migrar utilizadores entre licenças de produtos utilizando licenças baseadas em grupo no Azure Ative Directory](licensing-groups-change-licenses.md)
* [Cenários adicionais de licenciamento baseado no grupo do Azure Active Directory](./licensing-group-advanced.md)
* [Exemplos powerShell para licenciamento baseado em grupo no Azure Ative Directory](licensing-ps-examples.md)