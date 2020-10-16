---
title: Melhorias na gestão dos utilizadores (pré-visualização) - Azure Ative Directory Microsoft Docs
description: Descreve como o Azure Ative Directory permite a pesquisa, filtragem e mais informações sobre os seus utilizadores.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: how-to
ms.date: 10/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: be0d428120f53a4edb9763199a78b0e50409b19a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91708747"
---
# <a name="user-management-enhancements-preview-in-azure-active-directory"></a>Melhorias na gestão dos utilizadores (pré-visualização) no Azure Ative Directory

Este artigo descreve como utilizar a pré-visualização das melhorias de gestão do utilizador no portal Azure Ative Directory (Azure AD). As páginas **de todos os utilizadores** e **utilizadores eliminados** foram atualizadas para fornecer mais informações e facilitar a procura de utilizadores. Para obter mais informações sobre pré-visualizações, veja [Termos de Utilização Suplementares do Microsoft Azure para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

As alterações na pré-visualização incluem:

- Propriedades de utilizador mais visíveis, incluindo ID de objeto, estado de sincronização de diretório, tipo de criação e emitente de identidade
- A pesquisa agora permite pesquisa combinada de nomes, e-mails e iDs de objetos
- Filtragem melhorada por tipo de utilizador (membro, convidado, nenhum), estado de sincronização de diretório, tipo de criação, nome da empresa e nome de domínio
- Novas capacidades de triagem em propriedades como nome e nome principal do utilizador
- Um novo total de utilizadores conta que atualiza com pesquisas ou filtros

> [!NOTE]
> Esta pré-visualização não está disponível para inquilinos Azure AD B2C.

## <a name="find-the-preview"></a>Encontre a pré-visualização

A pré-visualização é ligada por defeito, para que possa usá-la imediatamente. Pode consultar as mais recentes funcionalidades e melhorias selecionando **funcionalidades de pré-visualização** na página **de todos os utilizadores.** Todas as páginas que tenham sido atualizadas como parte desta pré-visualização apresentarão uma etiqueta de pré-visualização. Se tiver algum problema, pode voltar à experiência do legado:

1. Inscreva-se no [centro de administração AdD Azure](https://aad.portal.azure.com) e selecione **Utilizadores**.
1. A partir da página **Utilizadores – Todos os utilizadores,** selecione o banner no topo da página.
1. No painel **de funcionalidades de pré-visualização,** desligue a gestão do **utilizador melhorada.**

   ![Como e onde ligar e desligar a Gestão avançada do utilizador](./media/users-search-enhanced/enable-preview.png)

Agradecemos o seu feedback para que possamos melhorar a nossa experiência.

## <a name="more-user-properties"></a>Mais propriedades do utilizador

Fizemos algumas alterações nas colunas disponíveis nas páginas de **todos os utilizadores** e **utilizadores eliminados.** Além das colunas existentes que fornecemos para gerir a sua lista de utilizadores, adicionámos mais algumas colunas.

### <a name="all-users-page"></a>Página de todos os utilizadores

Seguem-se as propriedades do utilizador visualizadas na página **de Todos os utilizadores:**

- Nome: O nome de exibição do utilizador.
- Nome principal do utilizador: O nome principal do utilizador (UPN) do utilizador.
- Tipo de utilizador: O tipo de utilizador do utilizador, membro ou convidado.
- Diretório sincronizado: Indica se o utilizador está sincronizado a partir de um diretório no local.
- Emitente de identidade: Os emitentes da identidade utilizados para assinar numa conta de utilizador.
- ID do objeto: O ID do objeto do utilizador.
- Tipo de criação: Indica como a conta de utilizador foi criada.
- Nome da empresa: O nome da empresa que o utilizador está associado.
- Estado do convite: O estado do convite para um utilizador convidado.
- Correio: O e-mail do utilizador.

![novas propriedades do utilizador apresentadas em Todos os utilizadores e páginas de utilizadores eliminados](./media/users-search-enhanced/user-properties.png)

### <a name="deleted-users-page"></a>Página de utilizadores eliminados

A página **de utilizadores eliminados** inclui todas as colunas disponíveis na página **de todos os utilizadores,** e algumas colunas adicionais, nomeadamente:

- Data de eliminação: A data em que o utilizador foi eliminado pela primeira vez da organização (o utilizador é restaurador).
- Data de eliminação permanente: A data em que o utilizador foi permanentemente eliminado da organização.

Algumas colunas são apresentadas por defeito. Para adicionar outras colunas, selecione **Colunas** na página, selecione os nomes das colunas que pretende adicionar e selecione **OK** para guardar as suas preferências.

### <a name="identity-issuers"></a>Emitentes de identidade

Selecione uma entrada na coluna do **emitente identidade** para qualquer utilizador visualizar detalhes adicionais sobre o emitente, incluindo o tipo de entrada e o iD do emitente atribuído. As entradas na coluna do **emitente identidade** podem ser multivalorizados. Se existirem vários emitentes da identidade do utilizador, verá a palavra Multiple na coluna **do emitente identidade** em **todos os utilizadores** e páginas **de utilizadores eliminados,** e os detalhes listam todos os emitentes.

> [!NOTE]
> A coluna **Fonte** é substituída por várias colunas, incluindo **o tipo de Criação,** **Diretório sincronizado,** e **Emitente de Identidade** para uma filtragem mais granular.

## <a name="user-list-search"></a>Pesquisa de lista de utilizadores

Quando introduz uma cadeia de pesquisa, a pesquisa utiliza a pesquisa "começa com" que agora pode combinar nomes, e-mails ou IDs de objeto numa única pesquisa. Pode introduzir qualquer um destes atributos na caixa de pesquisa, e a pesquisa procurará automaticamente em todas estas propriedades para devolver quaisquer resultados correspondentes. Pode efetuar a mesma pesquisa nas páginas **de Todos os utilizadores** e **utilizadores Eliminados.**

## <a name="user-list-filtering"></a>Filtragem da lista de utilizadores

As capacidades de filtragem foram melhoradas para fornecer mais opções de filtragem para **todas as páginas de utilizadores** e **utilizadores eliminados.** Agora pode filtrar por várias propriedades simultaneamente, e pode filtrar por mais propriedades.

### <a name="filtering-all-users-list"></a>Filtragem De todos os utilizadores lista

Seguem-se as propriedades filtrais na página **de Todos os utilizadores:**

- Tipo de utilizador: Membro, convidado, nenhum
- Estado sincronizado do diretório: Sim, não
- Tipo de criação: Convite, Email verificado, Conta Local
- Estado de convite – Aceitação pendente, Aceite
- Nome de domínio: Introduza um nome de domínio
- Nome da empresa: Introduza um nome da empresa
- Unidade administrativa: Selecione esta opção para restringir o âmbito dos utilizadores que vê a uma única unidade administrativa. Para mais informações, consulte [a pré-visualização da gestão de unidades administrativas.](directory-administrative-units.md)

### <a name="filtering-deleted-users-list"></a>Filtragem Lista de utilizadores eliminados

A página **de utilizadores eliminados** tem filtros adicionais não na página **de Todos os utilizadores.** Seguem-se as propriedades filtrais na página dos **utilizadores eliminados:**

- Tipo de utilizador: Membro, convidado, nenhum
- Estado sincronizado do diretório: Sim, não
- Tipo de criação: Convite, Email verificado, Conta Local
- Estado do convite: Aceitação pendente, Aceite
- Data de eliminação: Últimos 7, 14 ou 30 dias
- Nome de domínio: Introduza um nome de domínio
- Nome da empresa: Introduza um nome da empresa
- Data de supressão permanente: Últimos 7, 14 ou 30 dias

## <a name="user-list-sorting"></a>Classificação da lista de utilizadores

Pode agora ordenar pelo nome e nome principal do utilizador nas páginas **de Todos os utilizadores** e **utilizadores Eliminados.** Também pode ordenar por data de eliminação na lista **de Utilizadores Eliminados.**

## <a name="user-list-counts"></a>Contagem da lista de utilizadores

Pode ver o número total de utilizadores nas páginas **de Todos os utilizadores** e **utilizadores Eliminados.** À medida que pesquisa ou filtra as listas, a contagem é atualizada para refletir o número total de utilizadores encontrados.

![A ilustração da lista de utilizadores conta na página de Todos os utilizadores](./media/users-search-enhanced/user-list-sorting.png)

## <a name="frequently-asked-questions-faq"></a>Perguntas Frequentes (FAQ)

Pergunta | Resposta
-------- | ------
O que acontece com as capacidades a granel para utilizadores e hóspedes? | As operações a granel ainda estão disponíveis para utilizadores e hóspedes, incluindo criação a granel, convite a granel, exclusão a granel e utilizadores de descarregamento. Acabamos de fundi-los num menu chamado **Operações a Granel.** Pode encontrar as opções **de operações em massa** no topo da página de Todos os **utilizadores.**
O que aconteceu com a coluna Source? | A coluna **Fonte** foi substituída por outras colunas que fornecem informações semelhantes, permitindo ao mesmo tempo filtrar esses valores de forma independente. Exemplos incluem **tipo de Criação,** **Diretório sincronizado** e **emitente de identidade.**
O que aconteceu com a coluna User Name? | A coluna **Nome do Utilizador** ainda está lá, mas foi renomeada para Nome Principal do **Utilizador.** Isto reflete melhor a informação contida naquela coluna. Também notará que o nome principal do utilizador completo está agora apresentado para os hóspedes B2B. Isto coincide com o que se obtém no Ms Graph.  
Por que só posso fazer uma pesquisa de "começa com" e não uma pesquisa de "contém"? | Existem algumas limitações que nos impedem de permitir que você realize uma pesquisa "contém". Ouvimos o feedback, por isso fiquem atentos.

## <a name="next-steps"></a>Passos seguintes

Operações de utilizador

- [Adicionar ou alterar informações de perfil](../fundamentals/active-directory-users-profile-azure-portal.md)
- [Adicionar ou eliminar utilizadores](../fundamentals/add-users-azure-active-directory.md)

Operações a granel

- [Lista de utilizadores](users-bulk-download.md)
- [Utilizadores adicionados a granel](users-bulk-add.md)
- [Eliminar utilizadores em massa](users-bulk-delete.md)
- [Restaurar utilizadores em massa](users-bulk-restore.md)
