---
title: Ligue a Proteção de Informação Azure a Azure Sentinel
description: Transmita informações de Azure Information Protection para Azure Sentinel configurando o conector de dados Azure Information Protection.
services: sentinel
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: yelevin
ms.openlocfilehash: 82ea47037902ce3a9449f71a9edf62cb80863d4b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94655991"
---
# <a name="connect-data-from-azure-information-protection"></a>Ligar dados da Azure Information Protection

> [!IMPORTANT]
> O conector de dados da Azure Information Protection em Azure Sentinel encontra-se atualmente em pré-visualização pública.
> Esta funcionalidade é fornecida sem um contrato de nível de serviço, e não é recomendado para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pode transmitir informações de registo de registo do [Azure Information Protection](https://azure.microsoft.com/services/information-protection/) para o Azure Sentinel, configurando o conector de dados Azure Information Protection. O Azure Information Protection ajuda-o a controlar e a proteger os seus dados sensíveis, sejam armazenados na nuvem ou no local.

Se [o relatório central para a Proteção de Informações do Azure](/azure/information-protection/reports-aip) já estiver configurado para que as informações de registo deste serviço seja armazenadas no mesmo espaço de trabalho do Log Analytics que selecionou atualmente para o Azure Sentinel, pode saltar a configuração deste conector de dados. A informação de registo da Azure Information Protection já está disponível para o Azure Sentinel.

No entanto, se registar informações da Azure Information Protection vai para um espaço de trabalho diferente do que você selecionou atualmente para Azure Sentinel, faça um dos seguintes:

- Mude o espaço de trabalho selecionado no Azure Sentinel.

- Altere o espaço de trabalho para Azure Information Protection, o que pode fazer configurando este conector de dados.
    
    Se alterar o espaço de trabalho, novos dados de reporte para a Azure Information Protection serão agora armazenados no espaço de trabalho que está a usar para o Azure Sentinel, e os dados históricos não estão disponíveis para o Azure Sentinel. Além disso, se o espaço de trabalho anterior estiver configurado para consultas personalizadas, alertas ou APIs REST, estes devem ser reconfigurados para o espaço de trabalho Azure Sentinel se quiser continuar a usá-los para a Azure Information Protection. Não é necessária qualquer reconfiguração para clientes e serviços que utilizem a Proteção de Informação Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Uma das seguintes funções de administrador da AD Azure para o seu inquilino: 
    - Administrador de Proteção de Informação da Azure
    - Administrador de segurança
    - Administrador de conformidade
    - Administrador de dados de conformidade
    - Administrador global
    
    > [!NOTE]
    > Não é possível utilizar o papel de administrador de proteção de informação da Azure se o seu inquilino estiver na [plataforma de rotulagem unificada](/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform).
    
    Estas funções de administrador são necessárias apenas para configurar o conector Azure Information Protection, e não são necessárias quando o Azure Sentinel está ligado à Proteção de Informação Azure.

- Permissões para ler e escrever no espaço de trabalho Log Analytics que está a usar para a Azure Sentinel e Azure Information Protection.

- A Azure Information Protection foi adicionada ao portal Azure. Se precisar de ajuda com este passo, consulte [a Proteção de Informação Azure ao portal Azure](/azure/information-protection/quickstart-viewpolicy#add-azure-information-protection-to-the-azure-portal).

## <a name="connect-to-azure-information-protection"></a>Ligar à Proteção de Informação Azure

Utilize as seguintes instruções se ainda não tiver configurado um espaço de trabalho log Analytics para a Proteção de Informações Azure, ou se precisar de alterar o espaço de trabalho que armazena as informações de registo de registo da Proteção de Informação Azure.

1. Em Azure Sentinel, selecione **Data connectors**  >  **Azure Information Protection (Preview)**.

2. Selecione **Abrir a página do conector**.

3. Em **Configuração**, selecione **Connect Azure Information Protection .**

4. Na lâmina **Configure analytics (Preview),** selecione o espaço de trabalho que utiliza atualmente para Azure Sentinel. Se selecionar um espaço de trabalho diferente, os dados de reporte da Azure Information Protection não estão disponíveis para o Azure Sentinel.

5. Depois de selecionar um espaço de trabalho, selecione **OK**. O conector **STATUS** muda para **Connected**.

6. Os dados de reporte da Azure Information Protection são armazenados na tabela **InformationProtectionLogs_CL** no espaço de trabalho selecionado. 
    
    Para utilizar o esquema relevante no Azure Monitor para estes dados de reporte, procure **eventos de proteção de informação**. Para obter informações sobre estas funções do evento, consulte a [referência de esquema amigável para funções](/azure/information-protection/reports-aip#friendly-schema-reference-for-event-functions) de evento a partir da documentação Azure Information Protection.

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a ligar a Azure Information Protection ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)