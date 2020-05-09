---
title: Veja e pesquise a sua recente atividade de entrada na página My Sign-in (pré-visualização) - Diretório Ativo Azure / Microsoft Docs
description: Detalhes sobre como visualizar e pesquisar a sua recente atividade de inscrição na página My Sign-ins do portal My Account.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: rhicock
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: curtand
ms.openlocfilehash: c828878506ee69e90945d1f4be07eb81541b9757
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82625413"
---
# <a name="view-and-search-your-recent-sign-in-activity-from-the-my-sign-ins-preview-page"></a>Veja e procure a sua recente atividade de inscrição na página My Sign-ins (pré-visualização)

Pode ver todo o seu trabalho recente ou atividade de inscrição na conta escolar, a partir da página **My Sign-ins** do portal **My Account.** Rever o seu histórico de inscrições ajuda-o a verificar por atividades incomuns, ajudando-o a ver:

- Se alguém está a tentar adivinhar a tua senha.

- Se um intruso assinou com sucesso na sua conta, e a partir de que localização.

- Que aplicações o agressor tentou aceder.

## <a name="view-your-recent-sign-in-activity"></a>Ver a sua atividade de início de sessão recente

1. Inscreva-se na sua conta de trabalho https://myaccount.microsoft.com/ ou escola e depois vá para a sua página.

2. Selecione **My Sign-ins (pré-visualização)** do painel de navegação esquerdo ou selecione o link de **atividade recente da Revisão** a partir do bloco My **sign-ins (pré-visualização).**

    ![A página da minha Conta, mostrando links de atividade sintetizados](media/my-account-portal/my-account-portal-sign-ins.png)

3. Expandir e rever cada um dos itens de inscrição, certificando-se de que reconhece cada um deles. Se encontrar um item de entrada que não pareça familiar, recomendamos vivamente que altere a sua palavra-passe para ajudar a proteger a sua conta se esta estiver comprometida.

    ![Página de atividade recente com detalhes de inscrição expandidos](media/my-account-portal/my-account-portal-sign-ins-page.png)

### <a name="if-you-see-a-successful-sign-in"></a>Se vir um sessão de sucesso

Devia reconhecer a sua própria atividade como sendo normal. No entanto, se notar um sessão de acesso bem sucedido a partir de uma localização estranha, navegador ou sistema operativo, pode significar que um intruso teve acesso à sua conta. Nesta situação, recomendamos que altere imediatamente a sua palavra-passe e, em seguida, vá à página de [informações](https://mysignins.microsoft.com/security-info) de Segurança para atualizar as definições de segurança.

Antes de determinar que algo está incorreto, certifique-se de que não está a ver um falso positivo (onde o item parece questionável, mas está bem). Por exemplo, determinamos a sua localização e mapa aproximados com base no seu Endereço IP. As redes móveis são especialmente difíceis de identificar, uma vez que, por vezes, encaminham o tráfego através de locais distantes. Por isso, se assinou o uso do seu dispositivo móvel no estado de Washington, o local pode mostrar o sinal vindo da Califórnia. Por causa disso, sugerimos vivamente que verifique mais detalhes, além da localização. Também deve certificar-se de que o sistema operativo, o navegador e a aplicação também fazem sentido.

### <a name="if-you-see-an-unsuccessful-sign-in"></a>Se vir um insessão mal sucedido

Um inserção mal sucedido, sem atividade de sessão, significa que o seu método de verificação primária (nome de utilizador/palavra-passe) falhou. Isto pode significar que enevoou o seu nome de utilizador ou palavra-passe, mas também pode significar que um intruso estava a tentar adivinhar a sua palavra-passe. Se acha que foi um intruso a tentar adivinhar sem sucesso a sua palavra-passe, não tem de alterar a sua palavra-passe, mas sugerimos fortemente que se registe para a Autenticação Multi-Factor (MFA) do Azure. Com o MFA, mesmo que o hacker adivinhe eventualmente a sua palavra-passe, não será suficiente para aceder à sua conta.

Se vir um insessão mal sucedido, com uma nota no âmbito da atividade da Sessão que diz, **Verificação adicional falhou, código inválido,** significa que a sua autenticação primária (username/password) foi bem sucedida, mas o MFA falhou. Se isto fosse um intruso, eles adivinharam corretamente a sua palavra-passe, mas ainda não foram capazes de passar o desafio da MFA. Neste caso, recomendamos que ainda altere a sua palavra-passe, uma vez que o atacante acertou na peça e, em seguida, vá à página de [informações](https://mysignins.microsoft.com/security-info) de Segurança para atualizar as definições de segurança.

## <a name="search-for-specific-sign-in-activity"></a>Procurar atividade específica de iniciar sessão

Pode pesquisar a sua recente atividade de inscrição por qualquer uma das informações disponíveis. Por exemplo, pode pesquisar a sua recente atividade de sessão através do sistema operativo, localização, app, e assim por diante.

1. Na página de **atividades recentes do Reveja,** escreva as informações que pretende procurar na barra **de Pesquisa.** Por exemplo, `My Account` digite para procurar por toda a atividade recolhida pela aplicação My Account.

2. Selecione o botão **'Procurar'** para começar a procurar.

    ![Página de Atividades recentes, mostrando barra de pesquisa destacada, botão de pesquisa e resultados](media/my-account-portal/my-account-portal-sign-ins-page-search.png)

## <a name="next-steps"></a>Passos seguintes

Depois de ver a sua recente atividade de inscrição, pode:

- Veja ou gere a sua informação de [segurança.](user-help-security-info-overview.md)

- Veja ou gerencie os seus [dispositivos conectados](my-account-portal-devices-page.md).

- Ver ou gerir as suas [organizações.](my-account-portal-organizations-page.md)

- Veja como a sua organização [utiliza os seus dados relacionados com](my-account-portal-privacy-page.md)a privacidade.
