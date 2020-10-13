---
title: Configurar listas personalizadas de proteção de passwords do Azure Ative Directory
description: Neste tutorial, você aprende a configurar listas de proteção de senhas proibidas personalizadas para O Diretório Ativo Azure para restringir palavras comuns no seu ambiente.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/13/2020
ms.author: iainfou
author: iainfoulds
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 24e0cfa4227ba5d7c38449d3cf1304add5ae5c1f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88718888"
---
# <a name="tutorial-configure-custom-banned-passwords-for-azure-active-directory-password-protection"></a>Tutorial: Configurar senhas personalizadas proibidas para proteção de senha do Azure Ative Directory

Os utilizadores muitas vezes criam senhas que usam palavras locais comuns, como uma escola, uma equipa desportiva ou uma pessoa famosa. Estas palavras-passe são fáceis de adivinhar e fracas contra ataques baseados em dicionários. Para impor senhas fortes na sua organização, a lista de senhas proibidas por encomenda Azure Ative (Azure AD) permite-lhe adicionar cordas específicas para avaliar e bloquear. Um pedido de alteração de palavra-passe falha se houver uma correspondência na lista de senhas proibidas personalizadas.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Ativar senhas proibidas personalizadas
> * Adicione entradas à lista de senhas proibidas personalizadas
> * Teste alterações de senha com senha proibida

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, precisa dos seguintes recursos e privilégios:

* Um inquilino Azure AD em funcionamento com pelo menos um Azure AD Premium P1 ou uma licença de julgamento habilitada.
    * Se necessário, [crie um de graça.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Uma conta com privilégios *de administrador global.*
* Um utilizador não administrador com uma palavra-passe que conhece, como *testuser.* Você testa um evento de mudança de senha usando esta conta neste tutorial.
    * Se precisar de criar um utilizador, consulte [Quickstart: Adicione novos utilizadores ao Azure Ative Directory](../fundamentals/add-users-azure-active-directory.md).
    * Para testar a operação de alteração de palavra-passe utilizando uma palavra-passe proibida, o inquilino AD AZure deve ser [configurado para reposição da palavra-passe de autosserviço](tutorial-enable-sspr.md).

## <a name="what-are-banned-password-lists"></a>O que são listas de senhas proibidas?

A Azure AD inclui uma lista global de senhas proibidas. O conteúdo da lista global de senhas proibidas não se baseia em nenhuma fonte de dados externa. Em vez disso, a lista global de palavras-passe proibidas baseia-se nos resultados em curso da telemetria e análise de segurança Azure AD. Quando um utilizador ou administrador tenta alterar ou redefinir as suas credenciais, a palavra-passe desejada é verificada com a lista de senhas proibidas. O pedido de alteração de palavra-passe falha se houver uma correspondência na lista global de senhas proibidas. Não pode editar esta lista de palavras-passe global proibidas por defeito.

Para lhe dar flexibilidade em que palavras-passe são permitidas, também pode definir uma lista de palavras-passe proibidas personalizadas. A lista de senhas proibidas personalizadas funciona ao lado da lista global de senhas proibidas para impor senhas fortes na sua organização. Os termos específicos da organização podem ser adicionados à lista de palavras-passe proibidas sob medida, tais como os seguintes exemplos:

* Nomes de marcas
* Nomes dos produtos
* Localizações, como sede da empresa
* Termos internos específicos da empresa
* Abreviaturas que têm significado específico da empresa

Quando um utilizador tenta redefinir uma palavra-passe para algo que está na lista de palavras-passe proibidas global ou personalizada, vê uma das seguintes mensagens de erro:

* *Infelizmente, a sua palavra-passe contém uma palavra, frase ou padrão que torna a sua palavra-passe facilmente adivinhável. Por favor, tente novamente com uma senha diferente.*
* *Infelizmente, não podes usar essa palavra-passe porque contém palavras ou caracteres que foram bloqueados pelo teu administrador. Por favor, tente novamente com uma senha diferente.*

A lista de senhas proibidas personalizadas está limitada a um máximo de 1000 termos. Não foi concebido para bloquear grandes listas de senhas. Para maximizar os benefícios da lista de palavras-passe proibidas personalizadas, reveja os [conceitos de lista de senhas proibidas personalizadas](concept-password-ban-bad.md#custom-banned-password-list) e [a visão geral do algoritmo de avaliação de palavras-passe](concept-password-ban-bad.md#how-are-passwords-evaluated).

## <a name="configure-custom-banned-passwords"></a>Configurar senhas proibidas personalizadas

Vamos ativar a lista de senhas proibidas personalizadas e adicionar algumas entradas. Pode adicionar entradas adicionais à lista de palavras-passe proibidas sob medida a qualquer momento.

Para ativar a lista de senhas proibidas personalizadas e adicionar entradas à sua entrada, complete os seguintes passos:

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta com permissões *de administrador global.*
1. Procure e selecione **Azure Ative Directory,** em seguida, escolha **Segurança** no menu do lado esquerdo.
1. No **cabeçalho** do menu Gerir, selecione **métodos de autenticação**e, em seguida, **proteção por palavra-passe**.
1. Desa esta medida a opção para impor a **lista personalizada** para *Sim*.
1. Adicione cordas à **lista de palavras-passe proibidas por costume,** uma cadeia por linha. As seguintes considerações e limitações aplicam-se à lista de senhas proibidas por medida:

    * A lista de palavras-passe proibidas por medida pode conter até 1000 termos.
    * A lista de senhas proibidas por medida é insensidora.
    * A lista de palavras-passe proibidas personalizadas considera a substituição comum de caracteres, tais como "o" e "0", ou "a" e "@".
    * O comprimento mínimo da corda é de quatro caracteres, e o máximo é de 16 caracteres.

    Especifique as suas próprias palavras-passe personalizadas para proibir, como mostrado no exemplo seguinte

    [![Modifique a lista de palavras-passe proibidas sob métodos de autenticação no portal ](media/tutorial-configure-custom-password-protection/enable-configure-custom-banned-passwords-cropped.png) Azure](media/tutorial-configure-custom-password-protection/enable-configure-custom-banned-passwords.png#lightbox)

1. Deixe a opção de Ativar a **proteção de palavras-passe no Windows Server Ative Directory** para *o Nº*.
1. Para ativar as palavras-passe proibidas personalizadas e as suas entradas, **selecione Guardar**.

Pode levar várias horas para que as atualizações da lista de senhas proibidas personalizadas sejam aplicadas.

Para um ambiente híbrido, também pode [implementar a proteção de senha Azure AD para um ambiente no local.](howto-password-ban-bad-on-premises-deploy.md) As mesmas listas de senhas banidas global e personalizada são usadas tanto para pedidos de alteração de palavras-passe em nuvem como para pedidos de alteração de palavras-passe on-prem.

## <a name="test-custom-banned-password-list"></a>Teste lista de senhas proibidas personalizadas

Para ver a lista de senhas proibidas personalizadas em ação, tente alterar a palavra-passe para uma variação de uma que adicionou na secção anterior. Quando a Azure AD tenta processar a alteração da palavra-passe, a palavra-passe é igualada a uma entrada na lista de senhas proibidas personalizadas. Em seguida, é apresentado um erro ao utilizador.

> [!NOTE]
> Antes de um utilizador poder redefinir a sua palavra-passe no portal baseado na Web, o inquilino AZURE AD deve ser [configurado para reiniciar a palavra-passe de autosserviço](tutorial-enable-sspr.md). Se necessário, o utilizador pode [então https://aka.ms/ssprsetup registar-se na SSPR em ](https://aka.ms/ssprsetup).

1. Aceda à página **My Apps** em [https://myapps.microsoft.com](https://myapps.microsoft.com) .
1. No canto superior direito, selecione o seu nome e, em seguida, escolha **Profile** no menu suspenso.

    ![Selecionar perfil](media/tutorial-configure-custom-password-protection/myapps-profile.png)

1. Na página **'Perfil',** selecione **Alterar a palavra-passe.**
1. Na página de **senha de alteração,** introduza a palavra-passe existente (antiga). Introduza e confirme uma nova palavra-passe que está na lista de senhas proibidas personalizadas que definiu na secção anterior e, em seguida, **selecione Enviar por palavras-passe**.
1. É devolvida uma mensagem de erro que lhe diz que a palavra-passe foi bloqueada pelo administrador, como mostra o seguinte exemplo:

    ![Mensagem de erro exibida quando tenta usar uma palavra-passe que faz parte da lista de senhas proibidas personalizadas](media/tutorial-configure-custom-password-protection/password-change-error.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Se já não pretender utilizar a lista de palavras-passe proibidas personalizadas que configura como parte deste tutorial, complete os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Procure e selecione **Azure Ative Directory,** em seguida, escolha **Segurança** no menu do lado esquerdo.
1. No **cabeçalho** do menu Gerir, selecione **métodos de autenticação**e, em seguida, **proteção por palavra-passe**.
1. Desa esta medida a opção para impor a **lista personalizada** para *o Nº*.
1. Para atualizar a configuração de palavra-passe proibida personalizada, **selecione Guardar**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ativou e configurar listas de proteção de senhas personalizadas para Azure AD. Aprendeu a:

> [!div class="checklist"]
> * Ativar senhas proibidas personalizadas
> * Adicione entradas à lista de senhas proibidas personalizadas
> * Teste alterações de senha com senha proibida

> [!div class="nextstepaction"]
> [Ativar o Multi-Factor Authentication baseado no risco](./tutorial-enable-azure-mfa.md)