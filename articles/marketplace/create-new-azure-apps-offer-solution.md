---
title: Configure um plano de modelo de solução
description: Saiba como configurar um plano de modelo de solução para a sua oferta de aplicação Azure no Partner Center.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: 8469cad02009d054bd8ba97fb4aabfdae84ef842
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96744623"
---
# <a name="configure-a-solution-template-plan"></a>Configure um plano de modelo de solução

Este artigo aplica-se apenas aos planos de modelo de solução para uma oferta de aplicação Azure. Se estiver a configurar um plano de candidatura gerido, vá ao [Configure um plano de candidatura gerido](create-new-azure-apps-offer-managed.md).

## <a name="choose-who-can-see-your-plan"></a>Escolha quem pode ver o seu plano

Pode configurar cada plano para ser visível para todos ou apenas para um público específico. Você concede acesso a um público privado usando IDs de subscrição Azure com a opção de incluir uma descrição de cada ID de subscrição que você atribui. Pode adicionar um máximo de 10 IDs de subscrição manualmente ou até 10.000 IDs de subscrição usando um . Ficheiro CSV. Os IDs de assinatura Azure são representados como GUIDs e as letras devem ser minúsculas.

> [!NOTE]
> Se publicar um plano privado, pode mudar a sua visibilidade para público mais tarde. No entanto, uma vez publicado um plano público, não se pode mudar a sua visibilidade para privado.

No separador **Disponibilidade,** no **âmbito da visibilidade do Plano,** faça uma das seguintes:

- Para tornar o plano público, selecione o botão de opção **Público** (também conhecido como botão de _rádio)._
- Para tornar o plano privado, selecione o botão de opção **Private** e, em seguida, adicione os IDs de assinatura Azure manualmente ou com um ficheiro CSV.

    > [!NOTE]
    > Um público privado ou restrito é diferente do público de pré-visualização que definiu no **separador Pré-visualização.** Um público de pré-visualização pode aceder à sua oferta antes da sua publicação ao vivo no mercado. Embora a escolha do público privado se aplique apenas a um plano específico, o público de pré-visualização pode ver todos os planos (privados ou não) para fins de validação.

### <a name="manually-add-azure-subscription-ids-for-a-private-plan"></a>Adicione manualmente IDs de subscrição Azure para um plano privado

1. Sob **a visibilidade do Plano,** selecione o botão de opção **Private.**
1. Na caixa **de ID de subscrição Azure** que aparece, insira o ID de subscrição Azure do público que pretende conceder acesso a este plano privado. É necessário um mínimo de um ID de subscrição.
1. (Opcional) Insira uma descrição deste público na caixa **Descrição.**
1. Para adicionar outro ID de subscrição, selecione o link **Add ID (Max 10)** e repita os passos 2 e 3.

## <a name="use-a-csv-file-to-add-azure-subscription-ids-for-a-private-plan"></a>Use um . Ficheiro CSV para adicionar IDs de subscrição Azure para um plano privado

1. Sob **a visibilidade do Plano,** selecione o botão de opção **Private.**
1. Selecione a **ligação Export Audience (csv).**
1. Abra o . Ficheiro CSV e adicionar os IDs de subscrição Azure que pretende conceder acesso à oferta privada à coluna **ID.**
1. Opcionalmente, introduza uma descrição para cada público na coluna **Descrição.**
1. Adicione "SubscriptionId" na coluna **Tipo,** para cada linha com um ID de subscrição.
1. Salve o . Ficheiro CSV.
1. No separador **Disponibilidade,** em **visibilidade do Plano,** selecione o link **Import Audience (csv).**
1. Na caixa de diálogo que aparece, selecione **Sim**.
1. Selecione o . Ficheiro CSV e, em seguida, selecione **Abrir**. Aparece uma mensagem indicando que . O ficheiro CSV foi importado com sucesso.

### <a name="hide-your-plan"></a>Esconda o seu plano

Se o seu modelo de solução se destinar a ser implementado apenas indiretamente quando referenciado embora outro modelo de solução ou aplicação gerida, selecione a caixa de verificação no **plano Oculta** para publicar o seu modelo de solução, mas esconda-o dos clientes que procuram e navegam diretamente por ele.

**Selecione Guardar o rascunho** antes de continuar para a secção seguinte: Defina a configuração técnica.

## <a name="define-the-technical-configuration"></a>Definir a configuração técnica

No separador **de configuração técnica,** irá carregar o pacote de implementação que permite aos clientes implementar o seu plano e fornecer um número de versão para o pacote.

> [!NOTE]
> Este separador não será visível se optar por reutilizar pacotes a partir de outro plano no **separador de configuração** do Plano. Em caso afirmativo, vá [ver os seus planos.](#view-your-plans)

### <a name="assign-a-version-number-for-the-package"></a>Atribua um número de versão para o pacote

Na caixa **versão** fornece-se a versão atual da configuração técnica. Incremente esta versão sempre que publicar uma alteração nesta página. O número da versão deve estar no formato: inteiro.inteiro.inteiro. Por exemplo, `1.0.2`.

### <a name="upload-a-package-file"></a>Faça upload de um ficheiro de pacote

No **ficheiro Pacote (.zip)**, arraste o seu ficheiro de pacote para a caixa cinzenta ou selecione a **navegação para o(s)** link de ficheiros.

> [!NOTE]
> Se tiver um problema de upload de ficheiros, certifique-se de que a rede local não bloqueia o `https://upload.xboxlive.com` serviço utilizado pelo Partner Center.

### <a name="previously-published-packages"></a>Pacotes previamente publicados

Depois de publicar a sua oferta ao vivo, o sub-separador de **pacotes previamente publicado** aparece na página de **configuração Técnica.** Este separador lista todas as versões previamente publicadas da sua configuração técnica.

## <a name="view-your-plans"></a>Veja os seus planos

- **Selecione Guardar** o rascunho e, em seguida, na parte superior esquerda da página, selecione **Plan overview** para voltar à página geral do **Plano.**

Depois de criar um ou mais planos, verá o nome do seu plano, o ID do plano, o tipo de plano, a disponibilidade (Público ou Privado), o estado atual da publicação e quaisquer ações disponíveis no separador **Plano.**

As ações disponíveis na coluna de **ação** do separador **Plano** variam consoante o estado do seu plano, podendo incluir o seguinte:

- Se o estado do plano for **Projeto,** o link na coluna **ação** dirá **eliminar o projeto**.
- Se o estado do plano for **live,** o link na coluna **Action** será **stop selling plan** ou Sync private **audience**. O link **de audiência privada Sync** publicará apenas as alterações ao seu público privado, sem publicar quaisquer outras atualizações que possa ter feito à oferta.
- Para criar outro plano para esta oferta, no topo do separador **Plan,** selecione **+ Crie um novo plano.** Em seguida, repita os passos em [Como criar planos para a sua oferta de candidatura Azure](create-new-azure-apps-offer-plans.md). Caso contrário, se já acabou de criar planos, vá para a próxima secção: Próximos passos.

## <a name="next-steps"></a>Passos seguintes

- [Como testar e publicar a sua oferta de Aplicação Azure.](create-new-azure-apps-offer-test-publish.md)
- Saiba [como vender a sua oferta de Aplicação Azure](create-new-azure-apps-offer-marketing.md) através da Co-venda com a Microsoft e Revenda através de programas CSPs.
