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
ms.openlocfilehash: fc4207547a1f34fca1ef302626f1365572a236b9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102101312"
---
# <a name="protect-windows-admin-center-resources-with-security-center"></a>Proteja os recursos do Centro de Administração do Windows com o Centro de Segurança

O Windows Admin Center é uma ferramenta de gestão para os seus servidores Windows. É uma localização única para os administradores do sistema acederem à maioria das ferramentas de administração mais usadas. A partir do Windows Admin Center, pode embarcar diretamente nos seus servidores no local para o Centro de Segurança Azure. Em seguida, pode ver um resumo das suas recomendações de segurança e alertas diretamente na experiência do Windows Admin Center.

> [!NOTE]
> A subscrição do Azure e o espaço de trabalho associado do Log Analytics precisam de ter o Azure Defender ativado de forma a permitir a integração do Windows Admin Center.
> O Azure Defender é gratuito durante os primeiros 30 dias se ainda não o tiver utilizado anteriormente na subscrição e no espaço de trabalho. Para obter detalhes sobre preços na sua moeda de eleição e de acordo com a sua região, consulte [os preços do Security Center](https://azure.microsoft.com/pricing/details/security-center/).
>

Quando tiver acedido com sucesso a um servidor do Windows Admin Center para o Azure Security Center, pode:

* Ver alertas de segurança e recomendações dentro da extensão do Centro de Segurança no Windows Admin Center
* Veja a postura de segurança e recupere informações detalhadas adicionais dos servidores geridos do Windows Admin Center no Security Center dentro do portal Azure (ou através de uma API)

Ao combinar estas duas ferramentas, o Security Center torna-se o seu único painel de vidro para visualizar todas as suas informações de segurança, qualquer que seja o recurso: proteger o seu Windows Admin Center gerido servidores no local, os seus VMs e quaisquer cargas de trabalho paaS adicionais.

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
    * A solução Log Analytics do Security Center está ativada no espaço de trabalho. Esta solução fornece as funcionalidades do Azure Defender para *todos os* servidores e máquinas virtuais que reportam a este espaço de trabalho.
    * O Azure Defender para servidores está ativado na subscrição.
    * O agente Log Analytics está instalado no servidor e configurado para reportar ao espaço de trabalho selecionado. Se o servidor já reporta a outro espaço de trabalho, está configurado para reportar ao espaço de trabalho recém-selecionado também.

    > [!NOTE]
    > Pode levar algum tempo depois de embarcar para que as recomendações apareçam. Na verdade, dependendo da atividade do seu servidor, poderá não receber *quaisquer* alertas. Para gerar alertas de teste para testar os seus alertas está a funcionar corretamente, siga as instruções do [procedimento de validação do alerta](security-center-alert-validation.md).


## <a name="view-security-recommendations-and-alerts-in-windows-admin-center"></a>Ver recomendações de segurança e alertas no Windows Admin Center

Uma vez a bordo, pode ver os seus alertas e recomendações diretamente na área do Centro de Segurança Azure do Windows Admin Center. Clique numa recomendação ou um alerta para vê-los no portal Azure. Lá, você vai obter informações adicionais e aprender a remediar os problemas.

[![Recomendações e alertas do Centro de Segurança como visto no Windows Admin Center](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png)](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png#lightbox)

## <a name="view-security-recommendations-and-alerts-for-windows-admin-center-managed-servers-in-security-center"></a>Ver recomendações de segurança e alertas para servidores geridos do Windows Admin Center no Security Center
Do Centro de Segurança Azure:

* Para ver recomendações de segurança para todos os seus servidores do Windows Admin Center, abra o inventário de [ativos](asset-inventory.md) e filtre para o tipo de máquina que pretende investigar. selecione o separador **VMs e Computers.**

* Para visualizar alertas de segurança para todos os seus servidores do Windows Admin Center, abra **os alertas de Segurança**. Clique **em Filter** e certifique-se de que **apenas** "Non-Azure" está selecionado:

    :::image type="content" source="./media/windows-admin-center-integration/filtering-alerts-by-environment.png" alt-text="Filtrar alertas de segurança para servidores geridos pelo Windows Admin Center" lightbox="./media/windows-admin-center-integration/filtering-alerts-by-environment.png":::