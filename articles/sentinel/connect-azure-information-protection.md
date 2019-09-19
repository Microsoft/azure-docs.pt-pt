---
title: Conectando dados da proteção de informações do Azure à visualização do Azure Sentinel | Microsoft Docs
description: Saiba como conectar dados da proteção de informações do Azure no Azure Sentinel.
services: sentinel
documentationcenter: na
author: cabailey
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2019
ms.author: cabailey
ms.openlocfilehash: 0614d24b19ef39cebdf4cb47fdd2d44470ea59c0
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71067750"
---
# <a name="connect-data-from-azure-information-protection"></a>Conectar dados da proteção de informações do Azure

> [!IMPORTANT]
> O Azure Sentinel está atualmente em visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Você pode transmitir informações de log da [proteção de informações do Azure](https://azure.microsoft.com/services/information-protection/) para o Azure Sentinel Configurando o conector de dados da proteção de informações do Azure. A proteção de informações do Azure ajuda a controlar e proteger seus dados confidenciais, sejam eles armazenados na nuvem ou no local.

Se o [relatório central da proteção de informações do Azure](https://docs.microsoft.com/azure/information-protection/reports-aip) já estiver configurado para que as informações de log desse serviço sejam armazenadas no mesmo espaço de trabalho log Analytics que você selecionou atualmente para o Azure Sentinel, você poderá ignorar a configuração de Este conector de dados. As informações de log da proteção de informações do Azure já estão disponíveis para o Azure Sentinel.

No entanto, se as informações de log da proteção de informações do Azure estiverem indo para um espaço de trabalho Log Analytics diferente daquele selecionado no momento para o Azure Sentinel, siga um destes procedimentos:

- Altere o espaço de trabalho selecionado no Azure Sentinel.

- Altere o espaço de trabalho da proteção de informações do Azure, que você pode fazer Configurando esse conector de dados.
    
    Se você alterar o espaço de trabalho, novos dados de relatório para a proteção de informações do Azure agora serão armazenados no espaço de trabalho que você está usando para o Azure Sentinel, e os dados históricos não estarão disponíveis para o Azure Sentinel. Além disso, se o espaço de trabalho anterior estiver configurado para consultas personalizadas, alertas ou APIs REST, eles deverão ser reconfigurados para o espaço de trabalho do Azure Sentinel se você quiser usá-los para a proteção de informações do Azure. Nenhuma reconfiguração é necessária para clientes e serviços que usam a proteção de informações do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Uma das seguintes funções de administrador do Azure AD para seu locatário: Administrador da proteção de informações do Azure, administrador de segurança ou administrador global.
    
    > [!NOTE]
    > Você não poderá usar a função de administrador da proteção de informações do Azure se seu locatário estiver na [plataforma de rotulamento unificada](https://docs.microsoft.com/azure/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform).

- Permissões para ler e gravar no espaço de trabalho de Log Analytics que você está usando para sentinela e proteção de informações do Azure.

- A proteção de informações do Azure foi adicionada ao portal do Azure. Se precisar de ajuda com esta etapa, consulte [Adicionar proteção de informações do Azure ao portal do Azure](https://docs.microsoft.com/azure/information-protection/quickstart-viewpolicy#add-azure-information-protection-to-the-azure-portal).

## <a name="connect-to-azure-information-protection"></a>Conectar-se à proteção de informações do Azure

Use as instruções a seguir se você não tiver configurado um espaço de trabalho Log Analytics para a proteção de informações do Azure ou se precisar alterar o espaço de trabalho que armazena as informações de log da proteção de informações do Azure. 

1. No Azure Sentinel, selecione **conectores de dados**e, em seguida, **proteção de informações do Azure**.

2. Na folha **proteção de informações do Azure** , selecione a **página abrir conector**.

3. Na próxima folha, na seção **configuração** , selecione proteção de **informações do Azure** para acessar a **análise da proteção de informações do Azure**.

4. Na lista de espaços de trabalho disponíveis, selecione o espaço de trabalho que você está usando no momento para o Azure Sentinel. Se você selecionar um espaço de trabalho diferente, os dados de relatório da proteção de informações do Azure não estarão disponíveis para o Azure Sentinel.

5. Quando você selecionar um espaço de trabalho, selecione **OK** e o **status** do conector agora deverá mudar para **conectado**.

6. Os dados de relatório da proteção de informações do Azure são armazenados na tabela **InformationProtectionLogs_CL** dentro do espaço de trabalho selecionado. 
    
    Para usar o esquema relevante no Azure Monitor para esses dados de relatório, procure **InformationProtectionEvents**. Para obter informações sobre essas funções de evento, consulte a seção [referência de esquema amigável para funções de evento](https://docs.microsoft.com/azure/information-protection/reports-aip#friendly-schema-reference-for-event-functions) da documentação da proteção de informações do Azure.

## <a name="next-steps"></a>Passos Seguintes
Neste documento, você aprendeu a conectar a proteção de informações do Azure ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade de seus dados e ameaças potenciais](quickstart-get-visibility.md).
- Comece [a detectar ameaças com o Azure Sentinel](tutorial-detect-threats.md).
