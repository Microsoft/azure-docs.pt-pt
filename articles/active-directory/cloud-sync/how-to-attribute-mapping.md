---
title: Azure AD Connect cloud sync editor
description: Este artigo descreve como usar o editor de atributos.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/22/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 80a035f30294449a024bbde76df2d42ddc23396e
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98622716"
---
# <a name="azure-ad-connect-cloud-sync-attribute-mapping"></a>Azure AD Connect cloud sync mapping

O Azure AD Connect cloud sync introduziu uma nova funcionalidade, que lhe permitirá mapear facilmente atributos entre os objetos de utilizador/grupo no local e os objetos em Azure AD.  Esta funcionalidade foi adicionada à configuração de sincronização de nuvem.

Pode personalizar os mapeamentos de atributos padrão de acordo com as necessidades do seu negócio. Assim, pode alterar ou eliminar os mapeamentos de atributos existentes ou criar novos mapeamentos de atributos.  Para uma lista de atributos sincronizados veja [atributos sincronizados](../hybrid/reference-connect-sync-attributes-synchronized.md?context=azure%2factive-directory%2fcloud-provisioning%2fcontext%2fcp-context/hybrid/reference-connect-sync-attributes-synchronized.md).

## <a name="understanding-attribute-mapping-types"></a>Compreender tipos de mapeamento de atributos
Com os mapeamentos de atributos, você controla como os atributos são povoados em Azure AD.
Existem quatro tipos de mapeamento diferentes suportados:

- **Direto** – o atributo alvo é preenchido com o valor de um atributo do objeto ligado em AD.
- **Constante** – o atributo alvo é preenchido com uma cadeia específica especificada.
- **Expressão** - o atributo alvo é povoado com base no resultado de uma expressão semelhante ao script.
  Para obter mais informações, consulte [a escrita de expressões para atribuir mapeamentos.](reference-expressions.md)
- **Nenhum** - o atributo alvo é deixado sem modificação. No entanto, se o atributo alvo estiver sempre vazio, é preenchido com o valor padrão que especifica.

Juntamente com estes quatro tipos básicos, os mapeamentos de atributos personalizados suportam o conceito de uma atribuição de valor **padrão** opcional. A atribuição de valor predefinido garante que um atributo alvo é povoado com um valor se não houver um valor em Ad Azure ou no objeto alvo. A configuração mais comum é deixar este em branco.

## <a name="understanding-attribute-mapping-properties"></a>Compreender propriedades de mapeamento de atributos

Na secção anterior, já foi introduzido na propriedade do tipo de mapeamento de atributos.
Juntamente com esta propriedade, os mapeamentos de atributos também suportam os seguintes atributos:

- **Atributo de origem** - O atributo do utilizador a partir do sistema de origem (exemplo: Ative Directory).
- **Atributo-alvo** – O atributo do utilizador no sistema-alvo (exemplo: Azure Ative Directory).
- **Valor predefinido se nulo (opcional)** - O valor que será passado para o sistema-alvo se o atributo de origem for nulo. Este valor só será a provisionado quando um utilizador for criado. O "valor predefinido quando nulo" não será previsto ao atualizar um utilizador existente.  
- **Aplique este mapeamento**
  - **Sempre** – Aplique este mapeamento tanto na criação do utilizador como nas ações de atualização.
  - **Apenas durante a criação** - Aplique este mapeamento apenas nas ações de criação de utilizadores.

> [!NOTE]
> Este documento descreve como usar o portal Azure para mapear atributos.  Para obter informações sobre a utilização do Gráfico consulte [transformações](how-to-transformation.md)

## <a name="using-attribute-mapping"></a>Usando o mapeamento de atributos

Para utilizar a nova funcionalidade, siga os passos abaixo.

1.  No portal do Azure, selecione **Azure Active Directory**.
2.  Selecione **Azure Ad Connect**.
3.  **Selecione Gerir a sincronização de nuvens**.

    ![Gerir o provisionamento](media/how-to-install/install-6.png)

4. Em **Configuração,** selecione a sua configuração.
5. Selecione **Clique para editar mapeamentos**.  Isto abrirá o ecrã de mapeamento do atributo.

    ![Adicionar atributos](media/how-to-attribute-mapping/mapping-6.png)

6.  Clique **em Adicionar Atributo**.

    ![Tipo de mapeamento](media/how-to-attribute-mapping/mapping-1.png)

7. Selecione o **tipo de Mapeamento**.  Neste exemplo usamos a Expressão.
8.  Introduza a expressão na caixa.  Para este exemplo estamos a usar: `Replace([mail], "@contoso.com", , ,"", ,).`
9.  Introduza o atributo alvo.  Neste exemplo usamos ExtensionAttribute15.
10. Selecione quando aplicar isto e, em seguida, clique **em Aplicar**

    ![Editar mapeamentos](media/how-to-attribute-mapping/mapping-2a.png)

11. De volta ao ecrã de mapeamento do atributo deve ver o seu novo mapeamento de atributos.  
12. Clique **em Guardar o Esquema.**

    ![Salvar o Schema](media/how-to-attribute-mapping/mapping-3.png)

## <a name="test-your-attribute-mapping"></a>Teste o mapeamento do seu atributo

Para testar o mapeamento do seu atributo, pode utilizar [o provisionamento a pedido](how-to-on-demand-provision.md).  Do 

1. No portal do Azure, selecione **Azure Active Directory**.
2. Selecione **Azure Ad Connect**.
3. **Selecione Gerir o provisionamento**.
4. Em **Configuração,** selecione a sua configuração.
5. Em **Validação** clique no botão **'Disposição'.** 
6. No ecrã de provisionamento a pedido.  Introduza o **nome distinto** de um utilizador ou grupo e clique no botão **Provision.**  
7. Uma vez concluído, deverá ver um ecrã de sucesso e 4 caixas de verificação verdes indicando que foi a provisionada com sucesso.  

    ![Sucesso do provisionamento](media/how-to-attribute-mapping/mapping-4.png)

8. Em **Perform Action** clique Ver **detalhes**.  À direita, deve ver o novo atributo sincronizado e a expressão aplicada.

  ![Realizar ação](media/how-to-attribute-mapping/mapping-5.png)

## <a name="next-steps"></a>Passos Seguintes

- [O que é a sincronização de nuvem AZure AD Connect?](what-is-cloud-sync.md)
- [Expressãos de escrita para atributos-mapeamentos](reference-expressions.md)
- [Atributos que são sincronizados](../hybrid/reference-connect-sync-attributes-synchronized.md?context=azure%2factive-directory%2fcloud-provisioning%2fcontext%2fcp-context/hybrid/reference-connect-sync-attributes-synchronized.md)
