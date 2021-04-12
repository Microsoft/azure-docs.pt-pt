---
title: Adicionar e gerir aplicações AD AZure - Azure Marketplace
description: Saiba como adicionar e gerir as aplicações AD da Azure para o programa de marketplace comercial no Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 04/06/2021
author: varsha-sarah
ms.author: vavargh
ms.custom: contperf-fy21q2
ms.openlocfilehash: be527647466ad76455585e16baabb26e39e42193
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108482"
---
# <a name="add-and-manage-azure-ad-applications"></a>Adicionar e gerir aplicações AD Azure

**Funções adequadas**

- Proprietário
- Gestor

Pode permitir que aplicações ou serviços que fazem parte do Azure Ative Directory (Azure AD) da sua empresa acedam à sua conta Partner Center.

## <a name="add-existing-azure-ad-applications"></a>Adicionar aplicações AD Azure existentes

Para adicionar aplicações que já existem no Diretório Ativo Azure da sua empresa:

1. A partir da página **de Utilizadores** (nas **definições de Conta),** selecione **adicionar aplicações AD Azure**.
1. Selecione uma ou mais aplicações AD AZure da lista que aparece. Pode utilizar a caixa de pesquisa para procurar aplicações Azure AD específicas. Se selecionar mais de uma aplicação AD Azure para adicionar à sua conta Partner Center, deve atribuir-lhes a mesma função ou conjunto de permissões personalizadas. Para adicionar várias aplicações AD Azure com diferentes funções/permissões, repita estes passos para cada função ou conjunto de permissões personalizadas.
1. Quando terminar de selecionar aplicações AD Azure, **selecione Adicionar selecionado**.
1. Na secção **Funções,** especifique as funções ou permissões personalizadas para a(s) aplicação AD selecionada.
1. Selecione **Guardar**.

## <a name="add-new-azure-ad-applications"></a>Adicionar novas aplicações AD AZure

Se quiser conceder acesso ao Partner Center a uma nova conta de aplicação AZure AD, pode criar uma na secção **Utilizadores.** Isto criará uma nova conta na sua conta de trabalho da empresa (Azure AD inquilino), e não apenas na sua conta partner Center. Se estiver a utilizar principalmente esta aplicação AD AD para autenticação do Partner Center, e não precisar que os utilizadores acedam diretamente à sua informação, pode introduzir qualquer endereço válido para o **URL de Resposta** e App **ID URI,** desde que esses valores não sejam utilizados por qualquer outra aplicação AZure AD no seu diretório.

1. A partir da página **de Utilizadores** (nas **definições de Conta),** selecione **adicionar aplicações AD Azure**.
1. Na página seguinte, selecione **a aplicação New AD AZure**.
1. Introduza o **URL de resposta** para a nova aplicação AZure AD. Este é o URL onde os utilizadores podem iniciar sposição e utilizar a sua aplicação AD Azure (por vezes também conhecida como URL da app ou URL Sign-On). O *URL de resposta* não pode ter mais de 256 caracteres e deve ser único dentro do seu diretório.
1. Insira a **App ID URI** para a nova aplicação AZure AD. Este é um identificador lógico para a aplicação Azure AD que é apresentado quando um único pedido de inscrição é enviado para Azure AD. A *App ID URI* deve ser única para cada aplicação AD AZure no seu diretório. Esta identificação não pode ter mais de 256 caracteres. Para obter mais informações sobre o ID URI da app, consulte [integrar aplicações com o Azure Ative Directory](/azure/active-directory/develop/quickstart-modify-supported-accounts.md#change-the-application-registration-to-support-different-accounts).
1. Na secção **Funções,** especifique as funções ou permissões personalizadas para a aplicação Azure AD.
1. Selecione **Guardar**.

Depois de adicionar ou criar uma aplicação AD Azure, pode voltar à secção **de Utilizadores** e selecionar o nome da aplicação para rever as definições da aplicação, incluindo o ID do Cliente, ID do Cliente, URL de resposta e App ID URI.

## <a name="remove-an-azure-ad-application"></a>Remover uma aplicação AD AZure

Para remover uma aplicação da sua conta de trabalho (inquilino Azure AD), vá aos **Utilizadores** (nas **definições de Conta),** selecione a aplicação que pretende remover utilizando a caixa de verificação na coluna de extrema-direita e, em seguida, escolha **Remover** das ações disponíveis. Aparecerá uma janela pop-up para confirmar que pretende remover as aplicações selecionadas.

## <a name="manage-keys-for-an-azure-ad-application"></a>Gerir chaves para uma aplicação AD Azure

Se a sua aplicação AD Azure ler e escrever dados no Microsoft Azure AD, precisará de uma chave. Pode criar chaves para uma aplicação AD Azure editando as suas informações no Partner Center. Também pode remover as chaves que já não são necessárias.

1. A partir da página **Utilizadores** (nas **definições de Conta),** selecione o nome da aplicação AD Azure. Você verá todas as chaves ativas para a aplicação AD Azure, incluindo a data em que a chave foi criada e quando expirará 50.
1. Para remover uma chave que já não é necessária, selecione **Remover**.
1. Para adicionar uma nova chave, **selecione Adicione nova tecla**.
1. Verá um ecrã que mostra os **valores** de **ID** e Chave do Cliente . Certifique-se de imprimir ou copiar esta informação, uma vez que não poderá aceder novamente a ela depois de sair desta página.
1. Se pretender criar mais teclas, **selecione Adicione outra tecla**.
