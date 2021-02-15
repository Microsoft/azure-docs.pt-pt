---
title: Veja e pesque a sua recente atividade de inscrição a partir da página My Sign-ins - Azure Ative Directory | Microsoft Docs
description: Detalhes sobre como visualizar e pesquisar a sua recente atividade de acesso a partir da página My's Ins do portal My Account.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: rhicock
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 01/19/2021
ms.author: curtand
ms.openlocfilehash: 1062c8dfe416af2d39063bf71f39d52e151e28d3
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/10/2021
ms.locfileid: "100096077"
---
# <a name="view-and-search-your-recent-sign-in-activity-from-the-my-sign-ins-page"></a>Veja e pesque a sua atividade de inscrição recente a partir da página My's

Pode ver todo o seu trabalho recente ou atividade de inscrição na conta escolar, a partir da página **My's Ins** do portal **My Account.** Rever o seu histórico de inscrições ajuda-o a verificar se há atividades incomuns, ajudando-o a ver:

- Se alguém está a tentar adivinhar a tua senha.
- Se um agressor se inscreveu com sucesso na sua conta e a partir de que local.
- Que aplicações o agressor tentou aceder.

>[!Note]
> Se vir um erro ao iniciar sessão com uma conta pessoal da Microsoft, ainda pode iniciar sessão utilizando o nome de domínio da sua organização (como contoso.com) ou o **ID** do Inquilino da sua organização a partir de um dos seguintes URLs:
>
>   - https://myaccount.microsoft.com?tenantId=*your_domain_name*
>   - https://myaccount.microsoft.com?tenant=*your_tenant_ID*

## <a name="view-your-recent-sign-in-activity"></a>Ver a sua atividade de início de sessão recente

1. Inscreva-se na sua conta de trabalho ou escola e depois vá para a sua https://myaccount.microsoft.com/ página.

2. Selecione **os meus insímios** a partir do painel de navegação esquerdo ou selecione o link de **atividade recente** do bloco **de inscrições My.**

    ![Página da Minha Conta, mostrando destaque Nos recentes links de atividade](media/my-account-portal/my-account-portal-sign-ins.png)

3. Expanda e reveja cada um dos itens de inscrição, certificando-se de que reconhece cada um deles. Se encontrar um item de entrada que não pareça familiar, altere a sua palavra-passe para proteger a conta no caso de estar comprometida.

    ![Página de atividade recente com detalhes de inscrição expandido](media/my-account-portal-sign-ins-page/recent-activity.png)

### <a name="if-you-see-a-successful-sign-in"></a>Se vir um sucesso de sindes

Por vezes, ao rever a sua própria atividade normal de inscrição, pode ver um sing-in bem-sucedido a partir de um local desconhecido, navegador ou sistema operativo. Inscrições desconhecidas podem significar que um intruso teve acesso à sua conta. Se vir uma atividade que não autorizou, recomendamos que altere imediatamente a sua palavra-passe e, em seguida, vá à [informação de Segurança](https://mysignins.microsoft.com/security-info) para atualizar as suas definições de segurança.

Antes de determinar que algo está errado, certifique-se de que não está a ver um falso positivo (onde o item parece questionável, mas está bem). Por exemplo, determinamos a sua localização e mapa aproximados com base no seu endereço IP. As redes móveis são especialmente difíceis de identificar, uma vez que, por vezes, encaminham o tráfego através de locais distantes. Mesmo que assines o teu telemóvel no estado de Washington, a localização pode mostrar a inscrição vinda da Califórnia. Sugerimos vivamente que verifique detalhes além da localização. Certifique-se de que o sistema operativo, o navegador e a aplicação também fazem sentido.

### <a name="if-you-see-an-unsuccessful-sign-in"></a>Se vir um s-in in mal sucedido

Se vires uma sinstura infrutífera, pode significar que escreveste mal as tuas credenciais. Também pode significar que um agressor estava a tentar adivinhar a tua senha. Para responder a este risco, não precisa de alterar a sua palavra-passe, mas recomendamos que se registe para autenticação multi-factor Azure AD (MFA). Com a autenticação de vários fatores, mesmo que o hacker adivinhe a sua palavra-passe, não será suficiente para aceder à conta.

![Azulejo de inscrição mal sucedido](media/my-account-portal-sign-ins-page/unsuccessful.png)

Se vir uma sessão de sessão mal sucedida, com uma nota em **atividade de Sessão** que diz, `Additional verification failed, invalid code` significa que as suas credenciais de autenticação primária foram bem sucedidas, mas a autenticação de vários fatores falhou. Esta condição pode significar que um intruso adivinhou corretamente a sua palavra-passe, mas não foi capaz de passar o desafio de autenticação multi-factor. Recomendamos que ainda altere a sua palavra-passe, porque o intruso pode já ter essa, e vá à página [de informações de Segurança](https://mysignins.microsoft.com/security-info) para atualizar as definições de segurança.

## <a name="search-for-specific-sign-in-activity"></a>Pesquisa rumo a uma atividade específica de inscrição

Pode pesquisar a sua recente atividade de inscrição por qualquer informação disponível. Por exemplo, pode pesquisar a sua recente atividade de inscrição através do sistema operativo, localização, aplicação, e assim por diante.

1. Na página **de atividades recentes** do Rever, digite as informações que pretende procurar na barra de **Pesquisa.** Por exemplo, `Unsuccessful` escreva para procurar por todas as atividades de inscrição mal sucedidas recolhidas pela aplicação My Account.

2. Selecione o botão **Procurar** para começar a procurar.

    ![Página de Atividades recentes, mostrando barra de pesquisa, botão de pesquisa e resultados](media/my-account-portal-sign-ins-page/sign-in-search.png)

### <a name="confirm-unusual-activity"></a>Confirmar atividade incomum

Os sign-ins que são sinalizados como atividade invulgar podem ser confirmados no azulejo para essa atividade na página **My's.**

![Azulejo de entrada incomum para confirmar que tentou ou não o sinal de entrada](media/my-account-portal-sign-ins-page/this-wasnt-me.png)

## <a name="next-steps"></a>Passos seguintes

Depois de ver a sua recente atividade de inscrição, pode:

- Ver ou gerir a sua [informação de segurança.](./security-info-setup-signin.md)

- Ver ou gerir os [seus dispositivos ligados](my-account-portal-devices-page.md).

- Ver ou gerir as suas [organizações.](my-account-portal-organizations-page.md)

- Veja como a sua organização [utiliza os seus dados relacionados com a privacidade.](my-account-portal-privacy-page.md)

- Altere as [definições do portal 'Minha Conta'](my-account-portal-settings.md)