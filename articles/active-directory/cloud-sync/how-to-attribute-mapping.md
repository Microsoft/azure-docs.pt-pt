---
title: Mapeamento de atributos em sincronização de nuvem AD Connect Azure
description: Este artigo descreve como usar a funcionalidade de sincronização em nuvem do Azure AD Connect para os atributos do mapa.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/21/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: cdb043374cf6252da3929c8f0cda6c0a4be558b7
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102555215"
---
# <a name="attribute-mapping-in-azure-ad-connect-cloud-sync"></a>Mapeamento de atributos em sincronização de nuvem AD Connect Azure

Pode utilizar a funcionalidade de sincronização em nuvem do Azure Ative Directory (Azure AD) Connect para mapear atributos entre o utilizador ou objetos de grupo no local e os objetos em Azure AD. Esta capacidade foi adicionada à configuração de sincronização de nuvem.

Pode personalizar (alterar, excluir ou criar) os mapeamentos de atributos padrão de acordo com as necessidades do seu negócio. Para obter uma lista de atributos sincronizados, consulte [Atributos sincronizados ao Azure Ative Directory](../hybrid/reference-connect-sync-attributes-synchronized.md?context=azure%2factive-directory%2fcloud-provisioning%2fcontext%2fcp-context/hybrid/reference-connect-sync-attributes-synchronized.md).

## <a name="understand-types-of-attribute-mapping"></a>Compreender tipos de mapeamento de atributos
Com o mapeamento de atributos, você controla como os atributos são povoados em Azure AD. A Azure AD suporta quatro tipos de mapeamento:

- **Direto**: O atributo alvo é povoado com o valor de um atributo do objeto ligado no Ative Directory.
- **Constante**: O atributo do alvo é preenchido com uma cadeia específica que especifica.
- **Expressão**: O atributo alvo é povoado com base no resultado de uma expressão semelhante ao script. Para obter mais informações, consulte [expressões de escrita para mapeamentos de atributos no Azure Ative Directory](reference-expressions.md).
- **Nenhum**: O atributo alvo é deixado sem modificação. No entanto, se o atributo alvo estiver sempre vazio, é preenchido com o valor padrão que especifica.

Juntamente com estes tipos básicos, os mapeamentos de atributos personalizados suportam o conceito de uma atribuição de valor *padrão* opcional. A atribuição de valor predefinido garante que um atributo alvo é povoado com um valor se Azure AD ou o objeto alvo não tiver um valor. A configuração mais comum é deixar este em branco.

## <a name="understand-properties-of-attribute-mapping"></a>Compreender propriedades do mapeamento de atributos

Juntamente com a propriedade tipo, os mapeamentos de atributos suportam os seguintes atributos:

- **Atributo de origem**: O atributo do utilizador a partir do sistema de origem (exemplo: Ative Directory).
- **Atributo-alvo**: O atributo do utilizador no sistema-alvo (exemplo: Azure Ative Directory).
- **Valor predefinido se nulo (opcional)**: O valor que será passado para o sistema-alvo se o atributo de origem for nulo. Este valor só será a provisionado quando um utilizador for criado. Não será a provisionado quando estiver a atualizar um utilizador existente.  
- **Aplique este mapeamento:**
  - **Sempre:** Aplique este mapeamento tanto nas ações de criação do utilizador como na atualização.
  - **Apenas durante a criação**: Aplique este mapeamento apenas em ações de criação de utilizadores.

> [!NOTE]
> Este artigo descreve como usar o portal Azure para mapear atributos.  Para obter informações sobre a utilização do Microsoft Graph, consulte [Transformações](how-to-transformation.md).

## <a name="add-an-attribute-mapping"></a>Adicione um mapeamento de atributos

Para utilizar a nova capacidade, siga estes passos:

1.  No portal do Azure, selecione **Azure Active Directory**.
2.  Selecione **Azure Ad Connect**.
3.  **Selecione Gerir a sincronização de nuvens**.

    ![Screenshot que mostra a ligação para gerir a sincronização da nuvem.](media/how-to-install/install-6.png)

4. Em **Configuração,** selecione a sua configuração.
5. Selecione **Clique para editar mapeamentos**.  Este link abre o ecrã **de mapeamentos do Atributo.**

    ![Screenshot que mostra a ligação para adicionar atributos.](media/how-to-attribute-mapping/mapping-6.png)

6.  **Selecione Adicionar atributo**.

    ![Screenshot que mostra o botão para adicionar um atributo, juntamente com listas de atributos e tipos de mapeamento.](media/how-to-attribute-mapping/mapping-1.png)

7. Selecione o tipo de mapeamento. Para este exemplo, estamos a usar **a Expressão.**
8. Introduza a expressão na caixa. Para este exemplo, estamos a `Replace([mail], "@contoso.com", , ,"", ,)` usar.
9. Introduza o atributo alvo. Para este exemplo, estamos a usar **extensionAttribute15**.
10. Selecione quando aplicar este mapeamento e, em seguida, **selecione Aplicar**.

    ![Screenshot que mostra as caixas preenchidas para criar um mapeamento de atributos.](media/how-to-attribute-mapping/mapping-2a.png)

11. De volta ao ecrã de **mapeamentos do Atributo,** deverá ver o seu novo mapeamento de atributos.  
12. **Selecione Guardar o esquema**.

    ![Screenshot que mostra o botão De guardar o esquema.](media/how-to-attribute-mapping/mapping-3.png)

## <a name="test-your-attribute-mapping"></a>Teste o mapeamento do seu atributo

Para testar o seu mapeamento de atributos, pode utilizar [o provisionamento a pedido:](how-to-on-demand-provision.md) 

1. No portal do Azure, selecione **Azure Active Directory**.
2. Selecione **Azure Ad Connect**.
3. **Selecione Gerir o provisionamento**.
4. Em **Configuração,** selecione a sua configuração.
5. Em **Validação**, selecione o botão **Devisão de utilizador.** 
6. No ecrã **De provisão a pedido,** introduza o nome distinto de um utilizador ou grupo e selecione o botão **Provision.** 

   O ecrã mostra que o provisionamento está em andamento.

   ![Screenshot que mostra provisão em progresso.](media/how-to-attribute-mapping/mapping-4.png)

8. Após o provisionamento final, um ecrã de sucesso aparece com quatro marcas de verificação verdes. 

   Em **Ação Perform**, selecione **Ver detalhes**. À direita, deve ver o novo atributo sincronizado e a expressão aplicada.

   ![Screenshot que mostra sucesso e detalhes de exportação.](media/how-to-attribute-mapping/mapping-5.png)

## <a name="next-steps"></a>Passos seguintes

- [O que é a sincronização de nuvem AZure AD Connect?](what-is-cloud-sync.md)
- [Expressãos de escrita para mapeamentos de atributos](reference-expressions.md)
- [Atributos sincronizados com o Azure Active Directory](../hybrid/reference-connect-sync-attributes-synchronized.md?context=azure%2factive-directory%2fcloud-provisioning%2fcontext%2fcp-context/hybrid/reference-connect-sync-attributes-synchronized.md)
