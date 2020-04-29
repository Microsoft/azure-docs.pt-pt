---
title: Configure listas personalizadas de proteção de senhas do Diretório Ativo do Azure
description: Neste tutorial, você aprende a configurar listas de proteção de senhas proibidas personalizadas para o Diretório Ativo Azure para restringir palavras comuns no seu ambiente.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 02/27/2020
ms.author: iainfou
author: iainfoulds
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4abb15462689470c87e9cf5ba8d5be8af2e45bfd
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78252846"
---
# <a name="tutorial-configure-custom-banned-passwords-for-azure-active-directory-password-protection"></a>Tutorial: Configure senhas proibidas personalizadas para proteção de senhas de diretório ativo Azure

Os utilizadores geralmente criam senhas que usam palavras locais comuns, como uma escola, equipe de esportes ou pessoa famosa. Estas palavras-passe são fáceis de adivinhar, e fracas contra ataques baseados no dicionário. Para impor senhas fortes na sua organização, a lista de senhas proibidas personalizadas do Azure Ative Directory (Azure AD) permite-lhe adicionar cordas específicas para avaliar e bloquear. Um pedido de alteração de senha falha se houver uma correspondência na lista de senhas proibida seletiva.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Ativar senhas proibidas personalizadas
> * Adicione entradas na lista de senhas proibidas personalizadas
> * Teste alterações de senha com senha proibida

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, necessita dos seguintes recursos e privilégios:

* Um inquilino do Azure AD em funcionamento com, pelo menos, uma licença de avaliação ativada.
    * Se necessário, [crie um de graça.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Uma conta com privilégios *de administrador global.*
* Um utilizador não administrador com uma palavra-passe que conheça, como *testuser*. Testa um evento de mudança de senha usando esta conta neste tutorial.
    * Se precisar de criar um utilizador, consulte [Quickstart: Adicione novos utilizadores ao Diretório Ativo Do Azure](../add-users-azure-active-directory.md).
    * Para testar a operação de alteração de palavra-passe utilizando uma palavra-passe proibida, o inquilino da AD Azure deve ser [configurado para redefinir a palavra-passe de autosserviço](tutorial-enable-sspr.md).

## <a name="what-are-banned-password-lists"></a>O que são listas de senhas proibidas?

A Azure AD inclui uma lista global de senhas proibidas. O conteúdo da lista global de senhas proibidas não se baseia em nenhuma fonte de dados externo. Em vez disso, a lista global de senhas proibidas baseia-se nos resultados em curso da telemetria e análise de segurança da AD Azure. Quando um utilizador ou administrador tenta alterar ou redefinir as suas credenciais, a palavra-passe desejada é verificada na lista de senhas proibidas. O pedido de alteração de palavra-passe falha se houver uma correspondência na lista global de senhas proibidas.

Para lhe dar flexibilidade nas palavras-passe permitidas, também pode definir uma lista de senhas proibidas personalizadas. A lista de senhas proibidas personalizadas funciona ao lado da lista global de senhas proibidas para impor senhas fortes na sua organização. Termos específicos da organização podem ser adicionados à lista de senhas proibidas personalizadas, tais como os seguintes exemplos:

* Nomes de marcas
* Nomes do produto
* Localizações, tais como sede da empresa
* Termos internos específicos da empresa
* Abreviaturas que têm significado específico da empresa

Quando um utilizador tenta redefinir uma palavra-passe para algo que está na lista de senhas proibidas global ou personalizada, eles vêem uma das seguintes mensagens de erro:

* *Infelizmente, a sua palavra-passe contém uma palavra, frase ou padrão que torna a sua palavra-passe facilmente adiável. Por favor, tente de novo com uma senha diferente.*
* *Infelizmente, não pode usar essa palavra-passe porque contém palavras ou caracteres que foram bloqueados pelo seu administrador. Por favor, tente de novo com uma senha diferente.*

A lista de senhas proibida seletiva sélimitada a um máximo de 1000 termos. Não foi concebido para bloquear grandes listas de senhas. Para maximizar os benefícios da lista de palavras-passe proibida selada personalizada, reveja os conceitos de lista de [passwords proibidas personalizadas](concept-password-ban-bad.md#custom-banned-password-list) e a visão geral do algoritmo de [avaliação](concept-password-ban-bad.md#how-are-passwords-evaluated)de passwords.

## <a name="configure-custom-banned-passwords"></a>Configurar senhas proibidas personalizadas

Vamos ativar a lista de senhas proibida saldada e adicionar algumas entradas. Pode adicionar entradas adicionais à lista de senhas proibidas personalizadas a qualquer momento.

Para ativar a lista de senhas proibida saltivaee personalizada e adicionar-lhe entradas, complete os seguintes passos:

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta com permissões *de administrador global.*
1. Procure e selecione **Azure Ative Directory,** em seguida, escolha **segurança** do menu do lado esquerdo.
1. No cabeçalho do menu **Gerir,** selecione métodos de **autenticação,** em **seguida, proteção de palavra-passe**.
1. Detete a opção para impor a **lista personalizada** para *Sim*.
1. Adicione cordas à lista de **palavras-passe proibida**personalizada, uma cadeia por linha. As seguintes considerações e limitações aplicam-se à lista de senhas proibidas personalizadas:

    * A lista de senhas proibidas personalizadas pode conter até 1000 termos.
    * A lista de senhas proibida sé sensível a casos.
    * A lista de senhas proibidas personalizadas considera a substituição de caracteres comuns, tais como "o" e "0", ou "a" e "@".
    * O comprimento mínimo da corda é de quatro caracteres, e o máximo é de 16 caracteres.

    Especifique as suas próprias palavras-passe personalizadas para proibir, como mostra o seguinte exemplo

    [![](media/tutorial-configure-custom-password-protection/enable-configure-custom-banned-passwords-cropped.png "Modify the custom banned password list under Authentication Methods in the Azure portal")](media/tutorial-configure-custom-password-protection/enable-configure-custom-banned-passwords.png#lightbox)

1. Deixe a opção de proteção de **palavra-passe ativa no Diretório Ativo** do Servidor do Windows para *No*.
1. Para ativar as senhas proibidas personalizadas e as suas entradas, selecione **Guardar**.

Pode levar várias horas para que as atualizações da lista de senhas proibidas personalizadas sejam aplicadas.

Para um ambiente híbrido, também pode implementar a proteção de [senhas Azure AD para um ambiente no local.](howto-password-ban-bad-on-premises-deploy.md) As mesmas listas de senhas proibidas globais e personalizadas são usadas tanto para pedidos de alteração de palavras-passe em nuvem como em prem.

## <a name="test-custom-banned-password-list"></a>Lista de senha proibida personalizada de teste

Para ver a lista de senhas proibida si proibida em ação, tente alterar a palavra-passe para uma variação da que adicionou na secção anterior. Quando a AD Azure tenta processar a alteração da palavra-passe, a palavra-passe é correspondida a uma entrada na lista de senhas proibidas personalizadas. Em seguida, é apresentado um erro ao utilizador.

> [!NOTE]
> Antes de um utilizador poder redefinir a sua palavra-passe no portal baseado na Web, o inquilino da AD Azure deve ser [configurado para redefinir a palavra-passe de autosserviço](tutorial-enable-sspr.md).

1. Vá à página my [https://myapps.microsoft.com](https://myapps.microsoft.com) **apps** em .
1. No canto superior direito, selecione o seu nome e, em seguida, escolha **o Perfil** a partir do menu suspenso.

    ![Selecionar perfil](media/tutorial-configure-custom-password-protection/myapps-profile.png)

1. Na página **perfil,** selecione **Alterar a palavra-passe**.
1. Na página de **alterar palavra-passe,** introduza a senha (antiga) existente. Introduza e confirme uma nova senha que esteja na lista de senhas proibida seletiva mente definida na secção anterior e, em seguida, selecione **Enviar**.
1. É devolvida uma mensagem de erro que lhe diz que a palavra-passe foi bloqueada pelo administrador, como mostra o seguinte exemplo:

    ![Mensagem de erro exibida quando tenta usar uma palavra-passe que faz parte da lista de senhas proibidas personalizadas](media/tutorial-configure-custom-password-protection/password-change-error.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se já não quiser utilizar a lista de palavras-passe proibida personalizada que configuracomo parte deste tutorial, complete os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Procure e selecione **Azure Ative Directory,** em seguida, escolha **segurança** do menu do lado esquerdo.
1. No cabeçalho do menu **Gerir,** selecione métodos de **autenticação,** em **seguida, proteção de palavra-passe**.
1. Detete a opção para impor a **lista personalizada** para *No*.
1. Para atualizar a configuração de senha proibida personalizada, selecione **Guardar**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ativou e configurou listas de proteção de senhapersonalizadas para a AD Azure. Aprendeu a:

> [!div class="checklist"]
> * Ativar senhas proibidas personalizadas
> * Adicione entradas na lista de senhas proibidas personalizadas
> * Teste alterações de senha com senha proibida

> [!div class="nextstepaction"]
> [Ativar o Multi-Factor Authentication baseado no risco](tutorial-mfa-applications.md)
