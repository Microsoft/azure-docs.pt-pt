---
title: Como proteger servidores do Windows Admin Center com o Azure Security Center
description: Este artigo explica como integrar o Centro de Segurança Azure com o Windows Admin Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: memildin
ms.openlocfilehash: 90c1132091f543b84fc764522dfa95672f2e2aef
ms.sourcegitcommit: c94a177b11a850ab30f406edb233de6923ca742a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89277498"
---
# <a name="protect-windows-admin-center-resources-with-security-center"></a>Proteja os recursos do Centro de Administração do Windows com o Centro de Segurança

O Windows Admin Center é uma ferramenta de gestão para os seus servidores Windows. É uma localização única para os administradores do sistema acederem à maioria das ferramentas de administração mais usadas. A partir do Windows Admin Center, pode embarcar diretamente nos seus servidores on-prem no Azure Security Center. Em seguida, pode ver um resumo das suas recomendações de segurança e alertas diretamente na experiência do Windows Admin Center.

> [!NOTE]
> A subscrição do Azure e o espaço de trabalho associado do Log Analytics precisam de ter o nível padrão do Security Center ativado de forma a permitir a integração do Windows Admin Center.
> O nível padrão é gratuito durante os primeiros 30 dias se ainda não o tiver utilizado anteriormente na subscrição e no espaço de trabalho. Para mais informações, consulte [a página de informações sobre preços.](security-center-pricing.md)
>

Quando tiver acedido com sucesso a um servidor do Windows Admin Center para o Azure Security Center, pode:

* Ver alertas de segurança e recomendações dentro da extensão do Centro de Segurança no Windows Admin Center
* Veja a postura de segurança e recupere informações detalhadas adicionais dos servidores geridos do Windows Admin Center no Security Center dentro do portal Azure (ou através de uma API)

Ao combinar estas duas ferramentas, o Security Center torna-se o seu único painel de vidro para visualizar todas as suas informações de segurança, qualquer que seja o recurso: proteger o seu Windows Admin Center gerido por servidores on-prem, os seus VMs e quaisquer cargas de trabalho paaS adicionais.

## <a name="onboard-windows-admin-center-managed-servers-into-security-center"></a>A bordo do Windows Admin Center geriu servidores no Centro de Segurança

1. A partir do Windows Admin Center, selecione um dos seus servidores e, no painel **Ferramentas,** selecione a extensão do Centro de Segurança Azure:

    ![Extensão do Centro de Segurança Azure no Windows Admin Center](./media/windows-admin-center-integration/onboarding-from-wac.png)

    > [!NOTE]
    > Se o servidor já estiver a bordo do Centro de Segurança, a janela de configuração não aparecerá.

1. Clique **em iniciar sôm no Azure e instale.**
    ![Extensão do Centro de Administração do Windows para o Centro de Segurança Azure](./media/windows-admin-center-integration/onboarding-from-wac-welcome.png)

1. Siga as instruções para ligar o servidor ao Centro de Segurança. Depois de ter introduzido os detalhes necessários e confirmado, o Security Center faz as alterações de configuração necessárias para garantir que todas as seguintes são verdadeiras:
    * Um Gateway Azure está registado.
    * O servidor tem um espaço de trabalho para reportar e uma subscrição associada.
    * A solução padrão do Log Analytics do Security Center está ativada no espaço de trabalho. Esta solução fornece as funcionalidades de nível padrão do Security Center para *todos os* servidores e máquinas virtuais que reportam a este espaço de trabalho.
    * O preço padrão de nível do Security Center para a Máquina Virtual está ativado na subscrição.
    * O agente Log Analytics está instalado no servidor e configurado para reportar ao espaço de trabalho selecionado. Se o servidor já reporta a outro espaço de trabalho, está configurado para reportar ao espaço de trabalho recém-selecionado também.

    > [!NOTE]
    > Pode levar algum tempo depois de embarcar para que as recomendações apareçam. Na verdade, dependendo da atividade do seu servidor, poderá não receber *quaisquer* alertas. Para gerar alertas de teste para testar os seus alertas está a funcionar corretamente, siga as instruções do [procedimento de validação do alerta](security-center-alert-validation.md).


## <a name="view-security-recommendations-and-alerts-in-windows-admin-center"></a>Ver recomendações de segurança e alertas no Windows Admin Center

Uma vez a bordo, pode ver os seus alertas e recomendações diretamente na área do Centro de Segurança Azure do Windows Admin Center. Clique numa recomendação ou um alerta para vê-los no portal Azure. Lá, você vai obter informações adicionais e aprender a remediar os problemas.

[![Recomendações e alertas do Centro de Segurança como visto no Windows Admin Center](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png)](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png#lightbox)

## <a name="view-security-recommendations-and-alerts-for-windows-admin-center-managed-servers-in-security-center"></a>Ver recomendações de segurança e alertas para servidores geridos do Windows Admin Center no Security Center
Do Centro de Segurança Azure:

* Para ver recomendações de segurança para todos os seus servidores do Windows Admin Center, abra **as Aplicações de & Compute** e clique no separador **VMs e Computers.** Filtrar a lista por recurso "Servidor" como mostrado aqui:

    [![Ver recomendações de segurança para servidores geridos do Windows Admin Center](media/windows-admin-center-integration/viewing-recommendations-wac.png)](media/windows-admin-center-integration/viewing-recommendations-wac.png#lightbox)

* Para visualizar alertas de segurança para todos os seus servidores do Windows Admin Center, abra **os alertas de Segurança**. Clique **em Filter** e certifique-se de que **apenas** "Non-Azure" está selecionado:

    ![Filtrar alertas de segurança para servidores geridos pelo Windows Admin Center](./media/windows-admin-center-integration/filtering-alerts-to-non-azure.png)

    [![Ver alertas de segurança para servidores geridos do Windows Admin Center](media/windows-admin-center-integration/viewing-alerts-wac.png)](media/windows-admin-center-integration/viewing-alerts-wac.png#lightbox)