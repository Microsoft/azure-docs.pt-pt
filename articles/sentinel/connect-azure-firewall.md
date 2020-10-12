---
title: Ligue os dados do Azure Firewall ao Azure Sentinel
description: Saiba como ligar os dados do Azure Firewall ao Azure Sentinel.
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 08/30/2020
ms.author: yelevin
ms.openlocfilehash: 05a2869152def0fc9a44defaa21f0643d9fdbb08
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89504881"
---
# <a name="connect-data-from-azure-firewall"></a>Ligar dados do Azure Firewall

> [!IMPORTANT]
> O conector de dados Azure Firewall em Azure Sentinel está atualmente em pré-visualização pública.
> Esta funcionalidade é fornecida sem um contrato de nível de serviço, e não é recomendado para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O Azure Firewall é um serviço de segurança de rede gerido e com base na cloud que protege os recursos da Rede Virtual do Azure. É um firewall-as-service totalmente imponente com alta disponibilidade incorporada e escalabilidade de nuvem sem restrições. 

Pode ligar os registos do Azure Firewall ao Azure Sentinel, permitindo-lhe visualizar dados de registo em livros de trabalho, usá-lo para criar alertas personalizados e incorporá-lo para melhorar a sua investigação.

Saiba mais sobre [a monitorização dos registos do Azure Firewall](https://docs.microsoft.com/azure/firewall/tutorial-diagnostics).

## <a name="prerequisites"></a>Pré-requisitos

- Deve ter lido e escrito permissões no espaço de trabalho do Azure Sentinel.

## <a name="connect-to-azure-firewall"></a>Ligue-se à Firewall Azure
    
1. A partir do menu de navegação Azure Sentinel, selecione **Conectores de dados**.

1. Selecione **Azure Firewall** a partir da galeria de conectores de dados e, em seguida, selecione Abrir a Página do **Conector**  no painel de pré-visualização.

1. Ativar **registos de diagnóstico** em todas as firewalls cujos registos pretende ligar:

    1. Selecione o **recurso Open Azure Firewall >** link.

    1. A partir do menu de navegação **firewalls,** selecione **definições de Diagnóstico**.

    1. Selecione + Adicione a **definição** de diagnóstico na parte inferior da lista.

    1. No ecrã de **definições de Diagnóstico,** introduza um nome no campo **de nomes de definições de Diagnóstico.**
    
    1. Marque a caixa de verificação **Enviar para Registar Analytics.** Dois novos campos serão exibidos por baixo. Escolha o espaço de trabalho **de subscrição** e **log analytics** relevante (onde reside O Sentinela Azure).

    1. Marque as caixas de verificação dos tipos de regras cujos registos pretende ingerir. Recomendamos **AzureFirewallApplicationRule** e **AzureFirewallNetworkRule**.

    1. **Selecione Guardar** na parte superior do ecrã.

1. Para utilizar o esquema relevante em Alertas de Log Analytics, procure por **AzureDiagnostics**.

> [!NOTE]
>
> Com este conector de dados em particular, os indicadores de estado de conectividade (uma faixa de cor na galeria de conectores de dados e ícones de conexão ao lado dos nomes do tipo de dados) só mostrarão como *conectados* (verdes) se os dados ingerirem em algum ponto nas últimas duas semanas. Uma vez passadas duas semanas sem ingestão de dados, o conector mostrará como estando desligado. No momento em que mais dados chegam, o estado *ligado* voltará.

## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar os registos do Azure Firewall ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
