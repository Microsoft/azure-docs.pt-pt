---
title: Ligue os dados de proteção do Azure DDoS ao Azure Sentinel
description: Aprenda a ingerir dados de proteção Azure DDoS em Azure Sentinel, para que possa vê-los, analisá-los e investigá-los.
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 09/01/2020
ms.author: yelevin
ms.openlocfilehash: e8e44f69965af1987bd5f023644d966b3caf1c77
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89505878"
---
# <a name="connect-data-from-azure-ddos-protection"></a>Ligar dados da Azure DDoS Protection

> [!IMPORTANT]
> O conector de dados Azure DDoS Protection em Azure Sentinel está atualmente em pré-visualização pública.
> Esta funcionalidade é fornecida sem um contrato de nível de serviço, e não é recomendado para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Os ataques de negação de serviço distribuídos (DDoS) tentam esgotar os recursos de uma aplicação, tornando a aplicação indisponível para utilizadores legítimos. Os ataques de DDoS podem ser direcionadas para qualquer ponto final que esteja publicamente acessível através da internet. [A proteção Azure DDoS,](../virtual-network/ddos-protection-overview.md)combinada com as melhores práticas de design de aplicações, fornece uma defesa robusta contra os ataques de DDoS. Pode ligar os registos de Proteção Azure DDoS ao Azure Sentinel, permitindo-lhe visualizar dados de registo em livros de trabalho, usá-lo para criar alertas personalizados e incorporá-lo para melhorar as suas investigações. 

## <a name="prerequisites"></a>Pré-requisitos

- Deve ter lido e escrito permissões no espaço de trabalho do Azure Sentinel.

- Deve ter um plano de [proteção Azure DDoS Standard](../virtual-network/manage-ddos-protection.md#create-a-ddos-protection-plan)configurado.

- Deve ter uma rede virtual configurada [com a Norma Azure DDoS ativada](../virtual-network/manage-ddos-protection.md#enable-ddos-for-a-new-virtual-network).

## <a name="connect-to-azure-ddos-protection"></a>Ligue-se à Proteção DDoS Azure
    
1. A partir do menu de navegação Azure Sentinel, selecione **Conectores de dados**.

1. Selecione **Azure DDoS Protection** a partir da galeria de conectores de dados e, em seguida, selecione Abrir a Página do **Conector** no painel de pré-visualização.

1. Ativar **registos de diagnóstico** em todas as firewalls cujos registos pretende ligar:

    1. Selecione as **definições de Diagnóstico Aberto >** link e escolha um recurso de endereço IP **público** da lista.

    1. **Selecione + Adicione a definição de diagnóstico**.

    1. No ecrã de **definições de Diagnóstico:**
       - Introduza um nome no campo **de nome de definição de diagnóstico.**

       - Marque a caixa de verificação **Enviar para Registar Analytics.** Dois novos campos serão exibidos por baixo. Escolha o espaço de trabalho **de subscrição** e **log analytics** relevante (onde reside O Sentinela Azure).

       - Marque as caixas de verificação dos tipos de regras cujos registos pretende ingerir. Recomendamos **DDoSProtectionNotifications,** **DDoSMitigationFlowLogs**e **DDoSMitigationReports**.

    1. Clique em **Guardar** na parte superior do ecrã. Repita este processo para quaisquer firewalls adicionais (endereços IP públicos) para os quais tenha ativado a proteção do DDoS.

1. Para utilizar o esquema relevante no Log Analytics para alertas de proteção DDoS Azure, procure por **AzureDiagnostics**.

> [!NOTE]
>
> Com este conector de dados em particular, os indicadores de estado de conectividade (uma faixa de cor na galeria de conectores de dados e ícones de conexão ao lado dos nomes do tipo de dados) só mostrarão como *conectados* (verdes) se os dados ingerirem em algum ponto nas últimas duas semanas. Uma vez passadas duas semanas sem ingestão de dados, o conector mostrará como estando desligado. No momento em que mais dados chegam, o estado *ligado* voltará.

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a ligar os registos de Proteção Azure DDoS ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
