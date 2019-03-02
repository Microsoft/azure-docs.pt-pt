---
title: Ativar um piloto de SSPR do Azure AD
description: Neste tutorial, vai ativar a reposição personalizada de palavra-passe do Azure AD para um grupo piloto de utilizadores
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: b85284a5c2c3017e7ccc0a7e06a712e41c2a9561
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57215596"
---
# <a name="tutorial-complete-an-azure-ad-self-service-password-reset-pilot-roll-out"></a>Tutorial: Reposição de uma senha de autoatendimento do Azure AD completa implementação piloto

Neste tutorial, vai ativar uma implementação piloto de reposição personalizada de palavra-passe (SSPR) do Azure AD na sua organização e testá-la com uma conta de não administrador.

É importante que qualquer teste de reposição personalizada de palavra-passe seja feito com contas de não administrador. A Microsoft gere a política de reposição de palavra-passe para contas de administrador e requer a utilização de métodos de autenticação mais fortes. Esta política não permite a utilização de perguntas e respostas de segurança e requer a utilização de dois métodos para a reposição.

> [!div class="checklist"]
> * Ativar a reposição de palavras-passe self-service
> * Testar a SSPR como utilizador

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta de Administrador Global

## <a name="enable-self-service-password-reset"></a>Ativar a reposição de palavras-passe self-service

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta de Administrador Global.
1. Navegue até ao **Azure Active Directory** e selecione **Reposição de palavra-passe**.
1. Comece com um grupo piloto, ativando a reposição personalizada de palavra-passe para um subconjunto de utilizadores na sua organização.
   * Na página **Propriedades**, na opção **Reposição Personalizada de Palavra-passe Ativada**, escolha **Seleção** e selecione um grupo piloto.
      * Só os membros do grupo específico do Azure AD que escolher podem utilizar a funcionalidade SSPR. Recomendamos que defina um grupo de utilizadores e que utilize esta definição quando implementar a funcionalidade, para prova de conceito. O aninhamento de grupos de segurança é suportado aqui.
      * Certifique-se de que os utilizadores no grupo que escolheu estão adequadamente licenciados.
   * Clicar em **Guardar**
1. Na página **Métodos de autenticação**
   * Definir o **número de métodos necessários para repor** para **1**
   * Escolha os**Métodos disponíveis para os utilizadores** que a sua organização vai permitir. Para este tutorial, selecione as caixas para habilitar **E-Mail** e **código de aplicação móvel (pré-visualização)**.
   * Clicar em **Guardar**
1. Na página **Registo**
   * Selecione **Sim** para **Exigir que os utilizadores se registem ao iniciar sessão**.
   * Defina o **Número de dias antes de ser pedido aos utilizadores que voltem a confirmar as informações de autenticação** como **180**.
   * Clicar em **Guardar**
1. Na página **Notificações**
   * Defina a opção **Notificar os utilizadores sobre reposições de palavra-passe** como **Sim**.
   * Defina **Notificar todos os administradores quando outros administradores repõem as palavras-passe deles** como **Sim**.
1. Na página **Personalização**
   * A Microsoft recomenda que defina **Personalizar ligação de suporte técnico** como **Sim** e que forneça um endereço de e-mail ou o URL da página Web na qual os utilizadores possam obter ajuda adicional da sua organização no campo **E-mail ou URL de suporte técnico personalizado**.
   * Neste tutorial, deixaremos **Personalizar ligação de suporte técnico** definido como **Não**.

A reposição personalizada de palavra-passe está agora configurada para utilizadores da cloud no seu grupo piloto.

## <a name="test-sspr-as-a-user"></a>Testar a SSPR como utilizador

Teste a reposição personalizada de palavra-passe com um utilizador de teste não administrador que seja membro do seu grupo piloto. **Tenha em atenção que, se utilizar uma conta que tenha quaisquer funções de administrador atribuídas à mesma, os métodos e o número de autenticação podem ser diferentes daqueles que selecionou, pois a Microsoft gere a política de administrador.**

1. Abra uma nova janela de browser no modo InPrivate ou anónimo.
1. Com um registo de utilizador de teste para a reposição personalizada de palavra-passe através do portal de registo localizado em [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup).
1. Com o mesmo utilizador de teste, navegue para o portal de reposição personalizada de palavra-passe [https://aka.ms/sspr](https://aka.ms/sspr) e tente repor a palavra-passe com as informações que forneceu no passo anterior.
1. Deverá conseguir repor com êxito a palavra-passe.

## <a name="clean-up-resources"></a>Limpar recursos

Se decidir que já não quer utilizar a funcionalidade que configurou como parte deste tutorial, faça a alteração seguinte.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Navegue até ao **Azure Active Directory** e selecione **Reposição de palavra-passe**.
1. Na página **Propriedades**, na opção **Reposição Personalizada de Palavra-passe Ativada**, escolha **Nenhuma**.
1. Clicar em **Guardar**

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ativou a reposição personalizada de palavra-passe do Azure AD. Avance para o próximo tutorial para ver como uma infraestrutura do Active Directory Domain Services no local pode ser integrada na experiência de reposição personalizada de palavra-passe.

> [!div class="nextstepaction"]
> [Ativar a integração de repetição de escrita local de SSPR](tutorial-enable-writeback.md)
