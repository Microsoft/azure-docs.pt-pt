---
title: Ligue a Proteção de Informação Azure ao Sentinela Azure
description: Saiba como ligar dados de Proteção de Informação Azure no Azure Sentinel.
services: sentinel
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: yelevin
ms.openlocfilehash: 396fd7c4289c9d02d451b26f5fb6299acd31e2a4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77588557"
---
# <a name="connect-data-from-azure-information-protection"></a>Conectar dados da Proteção de Informação do Azure

> [!IMPORTANT]
> O conector de dados azure Information Protection em Azure Sentinel está atualmente em pré-visualização pública.
> Esta funcionalidade é fornecida sem um acordo de nível de serviço, e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

Pode transmitir informações de registo a partir da Proteção de [Informação do Azure](https://azure.microsoft.com/services/information-protection/) para o Azure Sentinel configurando o conector de dados de proteção de informação Azure. A Azure Information Protection ajuda-o a controlar e a proteger os seus dados sensíveis, quer sejam armazenados na nuvem ou no local.

Se o [relatório central para a Proteção de Informação do Azure](https://docs.microsoft.com/azure/information-protection/reports-aip) já estiver configurado para que as informações de registo deste serviço sejam armazenadas no mesmo espaço de trabalho do Log Analytics que você já selecionou para o Azure Sentinel, pode ignorar a configuração deste conector de dados. A informação sobre o registo da Azure Information Protection já está disponível para o Azure Sentinel.

No entanto, se o registo de informações do Azure Protection for para um espaço de trabalho diferente do log analytics do que aquele que você atualmente selecionou para O Sentinel, faça um dos seguintes:

- Altere o espaço de trabalho selecionado em Azure Sentinel.

- Altere o espaço de trabalho para a Proteção de Informação Azure, o que pode fazer configurando este conector de dados.
    
    Se alterar o espaço de trabalho, novos dados de reporte para a Proteção de Informação Do Azure serão agora armazenados no espaço de trabalho que está a usar para o Azure Sentinel, e os dados históricos não estão disponíveis para o Azure Sentinel. Além disso, se o espaço de trabalho anterior estiver configurado para consultas personalizadas, alertas ou APIs REST, estes devem ser reconfigurados para o espaço de trabalho Azure Sentinel se pretender continuar a usá-los para proteção de informação Azure. Não é necessária qualquer reconfiguração para clientes e serviços que utilizem a Proteção de Informação Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Uma das seguintes funções de administrador da AD Azure para o seu inquilino: 
    - Administrador de Proteção de Informação Azure
    - Administrador de segurança
    - Administrador de conformidade
    - Administrador de dados de conformidade
    - Administrador global
    
    > [!NOTE]
    > Não pode utilizar o papel de administrador de Proteção de Informação Azure se o seu inquilino estiver na [plataforma de rotulagem unificada.](/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform)
    
    Estas funções de administrador são necessárias apenas para configurar o conector de proteção de informação Azure, e não são necessárias quando o Azure Sentinel está ligado à Proteção de Informação Azure.

- Permissões para ler e escrever para o espaço de trabalho do Log Analytics que está a usar para a Proteção de Informação Azure Sentinel e Azure.

- A Azure Information Protection foi adicionada ao portal Azure. Se precisar de ajuda com este passo, consulte adicionar proteção de [informação Azure ao portal Azure](https://docs.microsoft.com/azure/information-protection/quickstart-viewpolicy#add-azure-information-protection-to-the-azure-portal).

## <a name="connect-to-azure-information-protection"></a>Ligar à Proteção de Informação Azure

Utilize as seguintes instruções se não tiver configurado um espaço de trabalho de Log Analytics para a Proteção de Informação Do Azure, ou se tiver de alterar o espaço de trabalho que armazena a informação de registo de informação do Azure.

1. No Azure Sentinel, selecione **conectores**de dados, e, em seguida, **Azure Information Protection (Pré-visualização)**.

2. Selecione página do **conector Aberto**.

3. Na lâmina de **análise Configure (Pré-visualização),** selecione o espaço de trabalho que está a utilizar atualmente para o Azure Sentinel. Se selecionar um espaço de trabalho diferente, os dados de reporte da Azure Information Protection não estarão disponíveis para o Azure Sentinel.

4. Quando tiver selecionado um espaço de trabalho, selecione **OK** e o **estado** do conector deve agora mudar para **Connected**.

5. Os dados reportados da Azure Information Protection são armazenados na tabela **InformationProtectionLogs_CL** dentro do espaço de trabalho selecionado. 
    
    Para utilizar o esquema relevante no Monitor Azure para estes dados de reporte, procure **InformationProtectionEvents**. Para obter informações sobre estas funções de evento, consulte a [referência de esquema amigável para funções](https://docs.microsoft.com/azure/information-protection/reports-aip#friendly-schema-reference-for-event-functions) de evento sabotada na documentação de Proteção de Informação Azure.

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a ligar a Proteção de Informação Azure ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Aprenda a [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começar [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
