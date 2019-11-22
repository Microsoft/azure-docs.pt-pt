---
title: Exiba e pesquise sua atividade de entrada recente na página meu logon (versão prévia)-Azure Active Directory | Microsoft Docs
description: Detalhes sobre como exibir e pesquisar sua atividade de entrada recente na página minhas entradas do portal minha conta.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: rhicock
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: a45caeed8b601a845b0eb0fbcd25f1ec097a77a1
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74279042"
---
# <a name="view-and-search-your-recent-sign-in-activity-from-the-my-sign-ins-preview-page"></a>Exibir e pesquisar sua atividade de entrada recente na página minhas entradas (versão prévia)

Você pode exibir todas as atividades recentes de entrada da conta corporativa ou de estudante, na página **minhas entradas** do portal **minha conta** . A revisão do histórico de entrada ajuda você a verificar atividades incomuns ajudando-o a ver:

- Se alguém estiver tentando adivinhar sua senha.

- Se um invasor tiver entrado com êxito em sua conta e a partir de qual local.

- Quais aplicativos o invasor tentou acessar.

## <a name="view-your-recent-sign-in-activity"></a>Ver a sua atividade de início de sessão recente

1. Entre em sua conta corporativa ou de estudante e, em seguida, vá para a página https://myprofile.microsoft.com/.

2. Selecione **minhas entradas (versão prévia)** no painel de navegação esquerdo ou selecione o link **examinar atividade recente** no bloco **minhas entradas (versão prévia)** .

    ![Página minha conta, mostrando links de atividade recentes realçados](media/my-account-portal/my-account-portal-sign-ins.png)

3. Expanda e examine cada um dos itens de entrada, garantindo que você reconheça cada um deles. Se você encontrar um item de entrada que não parece familiar, é altamente recomendável alterar sua senha para ajudar a proteger sua conta se ela tiver sido comprometida.

    ![Página atividade recente com detalhes de entrada expandida](media/my-account-portal/my-account-portal-sign-ins-page.png)

### <a name="if-you-see-a-successful-sign-in"></a>Se você vir uma entrada bem-sucedida

Você deve reconhecer sua própria atividade como normal. No entanto, se você notar uma entrada bem-sucedida de um local estranho, navegador ou sistema operacional, isso pode significar que um invasor obteve acesso à sua conta. Nessa situação, recomendamos que você altere imediatamente sua senha e, em seguida, vá para a página [informações de segurança](https://mysignins.microsoft.com/security-info) para atualizar as configurações de segurança.

Antes de determinar que algo está incorreto, verifique se você não está vendo um falso positivo (onde o item parece questionável, mas está ok). Por exemplo, determinamos o local e o mapa aproximados com base no seu endereço IP. As redes móveis são especialmente difíceis de identificar, pois, às vezes, roteiam o tráfego por meio de locais distantes. Portanto, se você tiver entrado usando seu dispositivo móvel no estado de Washington, o local poderá mostrar a entrada proveniente da Califórnia. Por isso, sugerimos enfaticamente que você verifique mais detalhes, além do local. Você também deve verificar se o sistema operacional, o navegador e o aplicativo fazem sentido.

### <a name="if-you-see-an-unsuccessful-sign-in"></a>Se você vir uma entrada malsucedida

Uma entrada malsucedida, sem atividade de sessão, significa que o método de verificação principal (nome de usuário/senha) falhou. Isso pode significar que você digitou indigitadamente seu nome de usuário ou senha, mas também pode significar que um invasor estava tentando adivinhar sua senha. Se você acha que foi um invasor tentando sem êxito adivinhar sua senha, não precisa alterar sua senha, mas sugerimos fortemente que você se registre na MFA (autenticação multifator do Azure). Com o MFA, mesmo que o hacker eventualmente Adivinhe sua senha, ele não será suficiente para acessar sua conta.

Se você vir uma entrada malsucedida, com uma observação sob a atividade de sessão que diz, a **verificação adicional falhou, código inválido**, significa que sua autenticação principal (nome de usuário/senha) foi bem-sucedida, mas a MFA falhou. Se esse era um invasor, ele adivinhou corretamente sua senha, mas ainda não conseguiu passar o desafio de MFA. Nesse caso, recomendamos que você ainda altere sua senha, uma vez que o invasor obteve a parte certa e, em seguida, vá para a página [informações de segurança](https://mysignins.microsoft.com/security-info) para atualizar suas configurações de segurança.

## <a name="search-for-specific-sign-in-activity"></a>Pesquisar atividade de entrada específica

Você pode pesquisar sua atividade de entrada recente por qualquer uma das informações disponíveis. Por exemplo, você pode pesquisar sua atividade de entrada recente por sistema operacional, local, aplicativo e assim por diante.

1. Na página **revisão recente da atividade** , digite as informações que você deseja pesquisar na barra de **pesquisa** . Por exemplo, digite `My Account` para pesquisar todas as atividades coletadas pelo aplicativo minha conta.

2. Selecione o botão **Pesquisar** para começar a Pesquisar.

    ![Página atividade recente, mostrando a barra de pesquisa realçada, o botão de pesquisa e os resultados](media/my-account-portal/my-account-portal-sign-ins-page-search.png)

## <a name="next-steps"></a>Passos seguintes

Depois de exibir sua atividade de entrada recente, você pode:

- Exiba ou gerencie suas [informações de segurança](user-help-security-info-overview.md).

- Exiba ou gerencie seus [dispositivos](my-account-portal-devices-page.md)conectados.

- Exiba ou gerencie suas [organizações](my-account-portal-organizations-page.md).

- Veja como sua organização [usa seus dados relacionados à privacidade](my-account-portal-privacy-page.md).
