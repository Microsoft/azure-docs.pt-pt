---
title: Veja e pesque a sua recente atividade de inscrição a partir da página My Sign-in (pré-visualização) - Azure Ative Directory / Microsoft Docs
description: Detalhes sobre como visualizar e pesquisar a sua recente atividade de acesso a partir da página My's Ins do portal My Account.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: rhicock
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 10/28/2019
ms.author: curtand
ms.openlocfilehash: 738e6507a1642a1ab76938eeaf3294668bcea964
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87422300"
---
# <a name="view-and-search-your-recent-sign-in-activity-from-the-my-sign-ins-preview-page"></a>Ver e pesquisar a sua atividade de sposição recente a partir da página My's (pré-visualização)

Pode ver todo o seu trabalho recente ou atividade de inscrição na conta escolar, a partir da página **My's Ins** do portal **My Account.** Rever o seu histórico de inscrições ajuda-o a verificar se há atividades incomuns, ajudando-o a ver:

- Se alguém está a tentar adivinhar a tua senha.

- Se um agressor se inscreveu com sucesso na sua conta e a partir de que local.

- Que aplicações o agressor tentou aceder.

## <a name="view-your-recent-sign-in-activity"></a>Ver a sua atividade de início de sessão recente

1. Inscreva-se na sua conta de trabalho ou escola e depois vá para a sua https://myaccount.microsoft.com/ página.

2. Selecione **Os Meus Ins de Sação (pré-visualização)** a partir do painel de navegação esquerdo ou selecione o link de **atividade recente** do bloco **de ins-ins (pré-visualização) do Meu.I.A.**

    ![Página da Minha Conta, mostrando destaque Nos recentes links de atividade](media/my-account-portal/my-account-portal-sign-ins.png)

3. Expanda e reveja cada um dos itens de inscrição, certificando-se de que reconhece cada um deles. Se encontrar um item de entrada que não pareça familiar, recomendamos vivamente que altere a sua palavra-passe para ajudar a proteger a sua conta se esta estiver comprometida.

    ![Página de atividade recente com detalhes de inscrição expandido](media/my-account-portal/my-account-portal-sign-ins-page.png)

### <a name="if-you-see-a-successful-sign-in"></a>Se vir um sucesso de sindes

Devia reconhecer a sua própria atividade como sendo normal. No entanto, se notar uma sing-in bem-sucedida a partir de uma localização, navegador ou sistema operativo estranho, pode significar que um intruso tenha tido acesso à sua conta. Nesta situação, recomendamos que altere imediatamente a sua palavra-passe e, em seguida, vá à página [de informações de Segurança](https://mysignins.microsoft.com/security-info) para atualizar as suas definições de segurança.

Antes de determinar que algo está errado, certifique-se de que não está a ver um falso positivo (onde o item parece questionável, mas está bem). Por exemplo, determinamos a sua localização e mapa aproximados com base no seu Endereço IP. As redes móveis são especialmente difíceis de identificar, uma vez que, por vezes, encaminham o tráfego através de locais distantes. Então, se se inscreveu usando o seu dispositivo móvel no estado de Washington, a localização pode mostrar o sinal vindo da Califórnia. Por causa disso, sugerimos fortemente que verifique mais detalhes, além da localização. Também deve certificar-se de que o sistema operativo, o navegador e a aplicação também fazem sentido.

### <a name="if-you-see-an-unsuccessful-sign-in"></a>Se vir um s-in sem sucesso

Um inserção infrutífero, sem atividade de sessão, significa que o seu método de verificação primária (nome de utilizador/palavra-passe) falhou. Isto pode significar que desmarça mal o seu nome de utilizador ou palavra-passe, mas também pode significar que um intruso estava a tentar adivinhar a sua palavra-passe. Se acha que foi um intruso a tentar adivinhar sem sucesso a sua palavra-passe, não tem de alterar a sua palavra-passe, mas sugerimos fortemente que se registe na Autenticação Multi-Factor (MFA) do Azure. Com o MFA, mesmo que o hacker adivinhe a sua palavra-passe, não será suficiente para aceder à sua conta.

Se vir um sessão de sessão mal sucedido, com uma nota na atividade de Sessão que diz que a **verificação adicional falhou, código inválido,** significa que a sua autenticação primária (nome de utilizador/palavra-passe) foi bem sucedida, mas a MFA falhou. Se isto foi um intruso, eles adivinharam corretamente a tua palavra-passe, mas mesmo assim não conseguiram passar o desafio da MFA. Neste caso, recomendamos que ainda altere a sua palavra-passe, uma vez que o intruso acertou na peça e, em seguida, vá à página [de informações de Segurança](https://mysignins.microsoft.com/security-info) para atualizar as suas definições de segurança.

## <a name="search-for-specific-sign-in-activity"></a>Pesquisa rumo a uma atividade específica de inscrição

Pode pesquisar a sua recente atividade de inscrição por qualquer informação disponível. Por exemplo, pode pesquisar a sua recente atividade de inscrição através do sistema operativo, localização, aplicação, e assim por diante.

1. Na página **de atividades recentes** do Rever, digite as informações que pretende procurar na barra de **Pesquisa.** Por exemplo, `My Account` escreva para procurar por toda a atividade recolhida pela aplicação My Account.

2. Selecione o botão **Procurar** para começar a procurar.

    ![Página de Atividades recentes, mostrando barra de pesquisa, botão de pesquisa e resultados](media/my-account-portal/my-account-portal-sign-ins-page-search.png)

## <a name="next-steps"></a>Próximos passos

Depois de ver a sua recente atividade de inscrição, pode:

- Ver ou gerir a sua [informação de segurança.](user-help-security-info-overview.md)

- Ver ou gerir os [seus dispositivos ligados](my-account-portal-devices-page.md).

- Ver ou gerir as suas [organizações.](my-account-portal-organizations-page.md)

- Veja como a sua organização [utiliza os seus dados relacionados com a privacidade.](my-account-portal-privacy-page.md)

- Altere as [definições do portal 'Minha Conta'](my-account-portal-settings.md)
