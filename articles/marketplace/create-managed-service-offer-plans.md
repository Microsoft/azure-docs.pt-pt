---
title: Como criar planos para a sua oferta de Serviço Gerido no Azure Marketplace
description: Saiba como criar planos para a sua oferta de Serviço Gerido no Azure Marketplace utilizando o Microsoft Partner Center.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 12/23/2020
ms.openlocfilehash: 9b5526af03bdbefeb54633c49bbd43743555f60b
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100383233"
---
# <a name="how-to-create-plans-for-your-managed-service-offer"></a>Como criar planos para a sua oferta de Serviço Gerido

As ofertas de Serviços Geridos vendidas através do mercado comercial da Microsoft devem ter pelo menos um plano. Você pode criar uma variedade de planos com diferentes opções dentro da mesma oferta. Estes planos (por vezes referidos como SKUs) podem diferir em termos de versão, rentabilização ou níveis de serviço. Para obter orientações detalhadas sobre planos, consulte [planos e preços para ofertas de mercado comercial.](./plans-pricing.md)

## <a name="create-a-plan"></a>Criar um plano

1. No separador **De visão geral** do Plano da sua oferta no Partner Center, selecione **+ Crie um novo plano.**
2. Na caixa de diálogo que aparece, no **âmbito do Plano de Identificação,** introduza um ID de plano único. Utilize até 50 caracteres alfanuméricos minúsculos, traços ou sublinhados. Não é possível modificar o ID do plano depois de selecionar **Criar**. Esta identificação será visível para os seus clientes.
3. Na caixa de **nomes do Plano,** insira um nome único para este plano. Use um máximo de 50 caracteres. Este nome será visível para os seus clientes.
4. Selecione **Criar**.

## <a name="define-the-plan-listing"></a>Definir a listagem de planos

No separador **de listagem do Plano,** defina o nome e descrição do plano como pretende que apareçam no mercado comercial.

1. A caixa **de nomes do Plano** apresenta o nome que forneceu anteriormente para este plano. Pode mudá-lo a qualquer momento. Este nome aparecerá no mercado comercial como o título do plano da sua oferta.
2. Na caixa de resumo do **Plano,** forneça uma breve descrição do seu plano, que pode ser usado em resultados de pesquisa no mercado.
3. Na caixa **de descrição** do Plano, explique o que torna este plano único e diferente de outros planos dentro da sua oferta.
4. **Selecione Guardar o rascunho** antes de continuar para o separador seguinte.

## <a name="define-pricing-and-availability"></a>Definir preços e disponibilidade

O único modelo de preços disponível para ofertas de Serviço Gerido é **trazer a sua própria licença (BYOL)**. Isto significa que cobra diretamente aos seus clientes por custos relacionados com esta oferta, e a Microsoft não lhe cobra quaisquer taxas.

Pode configurar cada plano para ser visível para todos (público) ou apenas para um público específico (privado).

> [!NOTE]
> Os planos privados não são suportados com subscrições estabelecidas através de um revendedor do programa Cloud Solution Provider (CSP).

> [!IMPORTANT]
> Uma vez que um plano tenha sido publicado como público, não se pode mudá-lo para privado. Para controlar quais os clientes que podem aceitar a sua oferta e delegar recursos, utilize um plano privado. Com um plano público, não é possível restringir a disponibilidade a determinados clientes ou mesmo a um determinado número de clientes (embora possa parar de vender completamente o plano se optar por fazê-lo). Pode remover o acesso a uma delegação depois de um cliente aceitar uma oferta apenas se tiver incluído uma Autorização com a Definição de Função definida para Função de Atribuição de Registo de Serviços Geridos Quando publicou a oferta. Também pode contactar o cliente e pedir-lhe que remova o seu acesso.

## <a name="make-your-plan-public"></a>Tornar o seu plano público

1. Sob **visibilidade do Plano,** selecione **Public**.
2. Selecione **Guardar rascunho**. Para voltar ao separador Plano, selecione **Plan overview** no canto superior esquerdo.
3. Para criar outro plano para esta oferta, selecione **+ Crie um novo plano** no separador **Plano.**

## <a name="make-your-plan-private"></a>Torne o seu plano privado

Você concede acesso a um plano privado usando IDs de assinatura Azure. Pode adicionar um máximo de 10 IDs de subscrição manualmente ou até 10.000 IDs de subscrição usando um . Ficheiro CSV.

Para adicionar manualmente até 10 IDs de subscrição:

1. Sob **a visibilidade do Plano,** selecione **Private**.
2. Insira o ID de subscrição Azure do público a que pretende conceder acesso.
3. Opcionalmente, insira uma descrição deste público na caixa **Descrição.**
4. Para adicionar outro ID, **selecione Add ID (Max 10)**.
5. Quando terminar de adicionar IDs, **selecione Guardar o rascunho**.

Para adicionar até 10.000 IDs de subscrição com um . Ficheiro CSV:

1. Sob **a visibilidade do Plano,** selecione **Private**.
2. Selecione a **ligação Export Audience (csv).** Isto irá descarregar um . Ficheiro CSV.
3. Abra o . Ficheiro CSV. Na coluna **Id,** insira os IDs de subscrição Azure aos que pretende conceder acesso.
4. Na coluna **Descrição,**   tem a opção de adicionar uma descrição para cada entrada.
5. Na ****   coluna Tipo, adicione **SubscriptionId**   a cada linha que tenha um ID.
6. Guarde o ficheiro como um . Ficheiro CSV.
7. No Partner Center, selecione a **ligação Import Audience (csv).**
8. Na ****   caixa de diálogo Confirmar, selecione **Sim** e, em seguida, carre deixe de carregar o . Ficheiro CSV.
9.  **Selecione Guardar o projeto**.

## <a name="technical-configuration"></a>Configuração técnica

Esta secção cria um manifesto com informação de autorização para a gestão dos recursos dos clientes. Estas informações são necessárias para permitir a [gestão de recursos delegados do Azure.](../lighthouse/concepts/azure-delegated-resource-management.md)

[Reveja os inquilinos, papéis e utilizadores em cenários do Farol de Azure](../lighthouse/concepts/tenants-users-roles.md#best-practices-for-defining-users-and-roles) para entender quais as funções que são suportadas e as melhores práticas para definir as suas autorizações.

> [!NOTE]
> Os utilizadores e funções nas suas entradas de Autorização aplicar-se-ão a todos os clientes que ativarem o plano. Se quiser limitar o acesso a um cliente específico, terá de publicar um plano privado para uso exclusivo.

### <a name="manifest"></a>Manifesto

1. Em **Manifesto,** forneça uma **versão** para o manifesto. Utilize o formato n.n.n (por exemplo, 1.2.5).
2. Insira o seu **ID do Inquilino.** Este é um GUID associado ao Azure Ative Directory (Azure AD) ID do inquilino da sua organização; ou seja, o inquilino gerente a partir do qual você vai aceder aos recursos dos seus clientes. Se não tiver este útil, pode encontrá-lo pairando sobre o nome da sua conta no lado superior direito do portal Azure, ou selecionando o **diretório da Switch**.

Se publicar uma nova versão da sua oferta e precisar de criar um manifesto atualizado, selecione **+ Novo manifesto**. Certifique-se de aumentar o número de versão da versão anterior do manifesto.

### <a name="authorizations"></a>Autorizações

As autorizações definem as entidades do seu inquilino gerente que podem aceder a recursos e subscrições para clientes que adquirem o plano. Cada uma destas entidades tem um papel incorporado que concede níveis específicos de acesso.

Pode criar até 20 autorizações para cada plano.

> [!TIP]
> Na maioria dos casos, irá querer atribuir funções a um grupo de utilizadores ou ao serviço principal do Azure, em vez de uma série de contas individuais de utilizador. Isto permite-lhe adicionar ou remover o acesso a utilizadores individuais sem ter de atualizar e reeditar o plano quando os seus requisitos de acesso mudarem. Ao atribuir funções a grupos AD Azure, [o tipo de grupo deve ser a Segurança e não o Office 365](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Para recomendações adicionais, consulte [Inquilinos, funções e utilizadores em cenários do Farol Azure.](../lighthouse/concepts/tenants-users-roles.md)

Para cada Autorização, terá de fornecer as seguintes informações. Pode então selecionar **+ Adicionar a autorização** quantas vezes for necessário para adicionar mais utilizadores e definições de função.

* **ID do objeto AAD**: o identificador Azure AD de um utilizador, grupo de utilizadores ou aplicação que será concedida determinadas permissões (conforme definido pela Definição de Função) aos recursos dos seus clientes.
* **Nome de visualização de objetos AAD:** um nome amigável para ajudar o cliente a compreender o propósito desta autorização. O cliente verá este nome ao delegar recursos.
* **Definição de função**: selecione uma das funções incorporadas Azure AD disponíveis da lista. Esta função determinará as permissões que o utilizador no campo principal de **ID** terá nos recursos dos seus clientes. Para descrições destes papéis, consulte [funções incorporadas](../role-based-access-control/built-in-roles.md) e [suporte de função para o Farol de Azure.](../lighthouse/concepts/tenants-users-roles.md#role-support-for-azure-lighthouse)

> [!NOTE]
> Como as novas funções incorporadas aplicáveis são adicionadas ao Azure, elas ficarão disponíveis aqui. Pode haver algum atraso antes de aparecerem.

* **Funções atribuíveis**: esta opção só aparecerá se tiver selecionado o Administrador de Acesso ao Utilizador na **Definição de Função** para esta autorização. Em caso afirmativo, deve adicionar uma ou mais funções atribuíveis aqui. O utilizador no campo **Azure AD Object ID** poderá atribuir estas funções a identidades geridas, o que é necessário para [implementar políticas que possam ser remediadas](../lighthouse/how-to/deploy-policy-remediation.md). Nenhuma outra permissão normalmente associada à função de Administrador de Acesso ao Utilizador será aplicada a este utilizador.

> [!TIP]
> Para garantir que pode [remover o acesso a uma delegação,](../lighthouse/how-to/remove-delegation.md) se necessário, inclua uma **Autorização** com a **Definição de Função** definida para [Atribuição de Registo de Serviços Geridos Eliminar Função](../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role). Se esta função não for atribuída, os recursos delegados só podem ser removidos por um utilizador no arrendatário do cliente.

Uma vez concluídas todas as secções para o seu plano, pode selecionar **+ Criar um novo plano** para criar planos adicionais. Quando terminar, **selecione Guardar o rascunho** antes de continuar.

## <a name="next-steps"></a>Passos seguintes

* [Rever e publicar](review-publish-offer.md)
