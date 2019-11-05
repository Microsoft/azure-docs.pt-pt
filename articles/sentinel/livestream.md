---
title: Use transmissão ao vivo de busca no Azure Sentinel para detectar ameaças | Microsoft Docs
description: Este artigo descreve como usar o transmissão ao vivo de busca no Azure Sentinel para manter o controle dos dados.
services: sentinel
documentationcenter: na
author: cabailey
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: cabailey
ms.openlocfilehash: d1d048507946850192c1c6f8ca9024c6f9ddf8ca
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515125"
---
# <a name="use-hunting-livestream-in-azure-sentinel-to-detect-threats"></a>Use transmissão ao vivo de caça no Azure Sentinel para detectar ameaças

> [!IMPORTANT]
> A caça transmissão ao vivo no Azure Sentinel está atualmente em visualização pública.
> Esse recurso é fornecido sem um contrato de nível de serviço e não é recomendado para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Use a busca de transmissão ao vivo para criar sessões interativas que permitem testar consultas recém-criadas à medida que ocorrem eventos, obter notificações das sessões quando uma correspondência for encontrada e iniciar investigações, se necessário. Você pode criar rapidamente uma sessão transmissão ao vivo usando qualquer consulta de Log Analytics.

- **Testar consultas recém-criadas conforme ocorrem eventos**
    
    Você pode testar e ajustar as consultas sem conflitos com as regras atuais que estão sendo aplicadas ativamente aos eventos. Depois de confirmar que essas novas consultas funcionam conforme o esperado, é fácil promovê-las para regras de alerta personalizadas, selecionando uma opção que eleva a sessão a um alerta.

- **Seja notificado quando as ameaças ocorrerem**
    
    Você pode comparar feeds de dados de ameaça a dados de log agregados e ser notificado quando ocorrer uma correspondência. Os feeds de dados de ameaça são fluxos contínuos de dados relacionados a ameaças potenciais ou atuais, portanto, a notificação pode indicar uma ameaça potencial à sua organização. Crie uma sessão transmissão ao vivo em vez de uma regra de alerta personalizada quando desejar ser notificado de um problema potencial sem as sobrecargas de manter uma regra de alerta personalizada.

- **Investigações de lançamento**
    
    Se houver uma investigação ativa que envolva um ativo, como um host ou usuário, você poderá exibir a atividade específica (ou qualquer) nos dados de log conforme ele ocorrer nesse ativo. Você pode ser notificado quando essa atividade ocorrer.


## <a name="create-a-livestream-session"></a>Criar uma sessão transmissão ao vivo

Você pode criar uma sessão transmissão ao vivo de uma consulta de busca existente ou criar sua sessão do zero.

1. Na portal do Azure, navegue até **sentinela** > **gerenciamento de ameaças** > **busca**.

2. Para criar uma sessão transmissão ao vivo de uma consulta de busca:
    
    1. Na guia **consultas** , localize a consulta de busca a ser usada.
    2. Clique com o botão direito do mouse na consulta e selecione **Adicionar a transmissão ao vivo**. Por exemplo:
    
    > [!div class="mx-imgBorder"]
    > ![criar sessão transmissão ao vivo da consulta de busca de sentinela do Azure](./media/livestream/livestream-from-query.png)

3. Para criar uma sessão transmissão ao vivo do zero: 
    
    1. Selecione a guia **transmissão ao vivo**
    2. Selecione **ir para transmissão ao vivo**.
    
4. Na folha **transmissão ao vivo** :
    
    - Se você iniciou o transmissão ao vivo de uma consulta, examine a consulta e faça as alterações que deseja fazer.
    - Se você iniciou o transmissão ao vivo do zero, crie sua consulta. 

5. Selecione **reproduzir** na barra de comandos.
    
    A barra de status na barra de comandos indica se o transmissão ao vivo está em execução ou em pausa. No exemplo a seguir, a sessão está em execução:
    
    > [!div class="mx-imgBorder"]
    > ![criar sessão transmissão ao vivo do Azure Sentinel caça](./media/livestream/livestream-session.png)

6. Selecione **salvar** na barra de comandos.
    
    A menos que você selecione **Pausar**, a sessão continuará a ser executada até que você saia da portal do Azure.

## <a name="view-your-livestream-sessions"></a>Exibir suas sessões do transmissão ao vivo

1. Na portal do Azure, navegue até **sentinela** > **gerenciamento de ameaças** > **caça** > guia **transmissão ao vivo** .

2. Selecione a sessão transmissão ao vivo que você deseja exibir ou editar. Por exemplo:
    
    > [!div class="mx-imgBorder"]
    > ![criar sessão transmissão ao vivo da consulta de busca de sentinela do Azure](./media/livestream/livestream-tab.png)
    
    A sessão transmissão ao vivo selecionada é aberta para reprodução, pausa, edição e assim por diante.

## <a name="receive-notifications-when-new-events-occur"></a>Receber notificações quando novos eventos ocorrerem

Como as notificações do transmissão ao vivo para novos eventos usam notificações de portal do Azure, você vê essas notificações sempre que usa o portal do Azure. Por exemplo:

![Notificação de portal do Azure para transmissão ao vivo](./media/livestream/notification.png)

Selecione a notificação para abrir a folha **transmissão ao vivo** .
 
## <a name="elevate-a-livestream-session-to-an-alert"></a>Elevar uma sessão transmissão ao vivo a um alerta

Você pode promover uma sessão transmissão ao vivo para um novo alerta selecionando **elevar para alerta** na barra de comandos na sessão transmissão ao vivo relevante:

> [!div class="mx-imgBorder"]
> ![elevar a sessão transmissão ao vivo a um alerta](./media/livestream/elevate-to-alert.png)

Essa ação abre o assistente de criação de regras, que é preenchido previamente com a consulta associada à sessão transmissão ao vivo.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu a usar o transmissão ao vivo de busca no Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:


- [Busca de ameaças de forma proativa](hunting.md)
- [Use blocos de anotações para executar campanhas de busca automatizadas](notebooks.md)
