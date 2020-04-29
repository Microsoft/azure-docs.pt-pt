---
title: Como integrar o Windows Admin Center com o Azure Security Center [ Microsoft Docs
description: Este artigo explica como integrar o Azure Security Center com o Windows Admin Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: cbbdf724b9d7fe4948553e7526410b994f491b49
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80435267"
---
# <a name="integrate-azure-security-center-with-windows-admin-center"></a>Integrar o Centro de Segurança Azure com o Windows Admin Center

O Windows Admin Center é uma ferramenta de gestão para os seus servidores Windows. É um único local para os administradores do sistema acederem à maioria das ferramentas de administração mais usadas. A partir do Windows Admin Center, pode embarcar diretamente nos seus servidores on-prem para o Azure Security Center. Em seguida, pode ver um resumo das suas recomendações de segurança e alertas diretamente na experiência do Windows Admin Center.

> [!NOTE]
> A subscrição do Azure e o espaço de trabalho associado do Log Analytics precisam de ter o nível padrão do Security Center ativado de forma a permitir a integração do Windows Admin Center.
> O nível padrão é gratuito durante os primeiros 30 dias se não o tiver usado anteriormente na subscrição e espaço de trabalho. Para mais informações, consulte [a página de informação sobre preços](security-center-pricing.md).
>

Quando tiver embarcado com sucesso num servidor do Windows Admin Center para o Azure Security Center, pode:

* Ver alertas de segurança e recomendações dentro da extensão do Centro de Segurança no Windows Admin Center
* Veja a postura de segurança e recupere informações detalhadas adicionais dos servidores geridos pelo Windows Admin Center no Centro de Segurança dentro do portal Azure (ou através de uma API)

Ao combinar estas duas ferramentas, o Security Center torna-se o seu único painel de vidro para ver todas as suas informações de segurança, qualquer que seja o recurso: proteger o seu Windows Admin Center gerido em servidores on-prem, os seus VMs e quaisquer cargas de trabalho adicionais do PaaS.

## <a name="onboarding-windows-admin-center-managed-servers-into-security-center"></a>Onboarding Windows Admin Center gerido servidores para o Centro de Segurança

1. No Windows Admin Center, selecione um dos seus servidores e no painel **Tools,** selecione a extensão do Centro de Segurança Azure:

    ![Extensão do Centro de Segurança Azure no Windows Admin Center](./media/windows-admin-center-integration/onboarding-from-wac.png)

    > [!NOTE]
    > Se o servidor já estiver a bordo do Centro de Segurança, a janela de configuração não aparecerá.

1. Clique **em Iniciar sessão no Azure e configurar**.
    ![Onboarding Windows Admin Center extensão para Centro de Segurança Azure](./media/windows-admin-center-integration/onboarding-from-wac-welcome.png)

1. Siga as instruções para ligar o seu servidor ao Centro de Segurança. Depois de ter introduzido os detalhes necessários e confirmado, o Security Center faz as alterações de configuração necessárias para garantir que todos os seguintes são verdadeiros:
    * Um Azure Gateway está registado.
    * O servidor tem um espaço de trabalho para reportar e uma subscrição associada.
    * A solução padrão de Log Analytics do Security Center está ativada no espaço de trabalho. Esta solução fornece funcionalidades padrão do Nível Standard do Security Center para *todos os* servidores e máquinas virtuais que reportam a este espaço de trabalho.
    * Os preços padrão do Nível de Segurança para Máquina Virtual estão ativados na subscrição.
    * O agente Log Analytics está instalado no servidor e configurado para reportar ao espaço de trabalho selecionado. Se o servidor já reportar a outro espaço de trabalho, está configurado para reportar ao espaço de trabalho recém-selecionado também.

    > [!NOTE]
    > Pode levar algum tempo após o embarque para que as recomendações apareçam. De facto, dependendo da atividade do seu servidor, poderá não receber *quaisquer* alertas. Para gerar alertas de teste para testar os seus alertas estão a funcionar corretamente, siga as instruções no procedimento de [validação de alerta](security-center-alert-validation.md).


## <a name="viewing-security-recommendations-and-alerts-in-windows-admin-center"></a>Visualização de recomendações de segurança e alertas no Windows Admin Center

Uma vez a bordo, pode ver os seus alertas e recomendações diretamente na área do Centro de Segurança Azure do Windows Admin Center. Clique numa recomendação ou num alerta para vê-las no portal Azure. Aí, você vai obter informações adicionais e aprender a remediar os problemas.

[![Recomendações e alertas do Centro de Segurança como visto no Windows Admin Center](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png)](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png#lightbox)

## <a name="viewing-security-recommendations-and-alerts-for-windows-admin-center-managed-servers-in-security-center"></a>Visualizar recomendações de segurança e alertas para o Windows Admin Center gerido servidores no Centro de Segurança
Do Centro de Segurança Azure:

* Para ver recomendações de segurança para todos os seus servidores do Windows Admin Center, abra **a Compute & Apps** e clique no separador **VMs e Computadores.** Filtrar a lista por recurso "Server" como mostrado aqui:

    [![Ver recomendações de segurança para servidores geridos pelo Windows Admin Center](media/windows-admin-center-integration/viewing-recommendations-wac.png)](media/windows-admin-center-integration/viewing-recommendations-wac.png#lightbox)

* Para visualizar alertas de segurança para todos os seus servidores do Windows Admin Center, abra **alertas**de Segurança . Clique em **Filtro** e certifique-se de que **só** é selecionado "Non-Azure":

    ![Filtrar alertas de segurança para servidores geridos pelo Windows Admin Center](./media/windows-admin-center-integration/filtering-alerts-to-non-azure.png)

    [![Ver alertas de segurança para servidores geridos pelo Windows Admin Center](media/windows-admin-center-integration/viewing-alerts-wac.png)](media/windows-admin-center-integration/viewing-alerts-wac.png#lightbox)