---
title: Segurança aprimorada da camada Standard-central de segurança do Azure
description: " Saiba como integrar ao Standard da central de segurança do Azure para aumentar a segurança. "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/19/2019
ms.author: memildin
ms.openlocfilehash: db74415d3c6c5f6636a150d212bc2bc3d790fbec
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686378"
---
# <a name="onboarding-to-azure-security-center-standard-for-enhanced-security"></a>Integração ao Standard da central de segurança do Azure para segurança aprimorada
Atualize para a central de segurança Standard para aproveitar o gerenciamento de segurança aprimorado e a proteção contra ameaças para suas cargas de trabalho de nuvem híbrida. Você pode experimentar o padrão gratuito. Consulte a página de [preços](https://azure.microsoft.com/pricing/details/security-center/) da central de segurança para obter mais informações.

A central de segurança padrão inclui:

- **Segurança híbrida** – obtenha uma exibição unificada de segurança em todas as suas cargas de trabalho locais e na nuvem. Aplique políticas de segurança e avalie continuamente a segurança de suas cargas de trabalho de nuvem híbrida para garantir a conformidade com os padrões de segurança. Recolha, pesquise e analise os dados de segurança de várias origens, incluindo firewalls e outras soluções de parceiros.
- **Detecção avançada de ameaças** -use a análise avançada e a gráfico de segurança inteligente da Microsoft para obter uma borda sobre ataques cibernéticos em evolução. Tire partido da análise comportamental e do machine learning incorporados para identificar ataques e explorações de dia zero. Monitorize redes, máquinas e serviços cloud para ataques recebidos e atividade pós-falhas. Uniformize a investigação com ferramentas interativas e informações sobre ameaças contextuais.
- **Controles de acesso e de aplicativo** – bloqueie malware e outros aplicativos indesejados aplicando recomendações de lista de permissões adaptadas para suas cargas de trabalho específicas e alimentadas pelo Machine Learning. Reduza a superfície de ataque de rede com acesso controlado just-in-time a portas de gerenciamento em VMs do Azure, reduzindo drasticamente a exposição à força bruta e a outros ataques de rede.

## <a name="detecting-unprotected-resources"></a>Detectando recursos desprotegidos
O Centro de Segurança deteta automaticamente quaisquer subscrições ou áreas de trabalho do Azure não ativadas para o Centro de Segurança Standard. Isto inclui as subscrições do Azure que utilizam o Centro de Segurança Gratuito e as áreas de trabalho que não têm a solução de Segurança ativada.

Você pode atualizar uma assinatura inteira do Azure para a camada Standard, que é herdada por todos os recursos com suporte na assinatura. A aplicação da camada Standard a um espaço de trabalho se aplica a todos os recursos que se reportam ao espaço de trabalho.

> [!NOTE]
> Talvez você queira gerenciar seus custos e limitar a quantidade de dados coletados para uma solução, limitando-o a um determinado conjunto de agentes. O [direcionamento de solução](../operations-management-suite/operations-management-suite-solution-targeting.md) permite que você aplique um escopo à solução e direcione um subconjunto de computadores no espaço de trabalho. Se você estiver usando o direcionamento de solução, a central de segurança listará o espaço de trabalho como não tendo uma solução.
>
>

## <a name="upgrade-an-azure-subscription-or-workspace"></a>Atualizar uma assinatura ou um espaço de trabalho do Azure
Para atualizar uma assinatura ou um espaço de trabalho para o Standard:
1. No menu principal do Centro de Segurança, selecione **Introdução**.
  ![Introdução](./media/security-center-onboarding/get-started.png)
2. Em **Atualização**, o Centro de Segurança apresenta uma lista de subscrições e áreas de trabalho elegíveis para inclusão. 
   - Pode clicar em **Aplicar a sua versão de avaliação** para ver uma lista de todas as subscrições e áreas de trabalho com o respetivo estado de elegibilidade de avaliação.
   -    Pode atualizar as subscrições e áreas de trabalho que não são elegíveis para avaliação.
   -    Pode selecionar as áreas de trabalho e subscrições elegíveis para iniciar a avaliação.
3.  Clique em **Iniciar avaliação** para iniciar a avaliação nas subscrições selecionadas.
  ![selecionar](./media/security-center-onboarding/select-subscription.png) de assinatura


   > [!NOTE]
   > Os recursos gratuitos da central de segurança são aplicados somente às VMs do Azure e VMSS. Os recursos gratuitos não são aplicados aos computadores não Azure. Se você selecionar Standard, os recursos padrão serão aplicados a todas as VMs do Azure, conjuntos de dimensionamento de VM e computadores não Azure que relatam para o espaço de trabalho. Recomendamos que você aplique o padrão para fornecer segurança avançada para seus recursos do Azure e não Azure.
   >

## <a name="onboard-non-azure-computers"></a>Carregar computadores não Azure
O Centro de Segurança pode monitorizar a postura de segurança dos seus computadores não Azure, mas, primeiro, tem de incluir esses recursos. Você pode adicionar computadores não Azure da folha **introdução** ou da folha **computação** . Vamos percorrer os dois métodos.

### <a name="add-new-non-azure-computers-from-getting-started"></a>Adicionar novos computadores não Azure da **Guia de introdução**

1. Volte para a **introdução**.
2. Selecione o separador **Introdução**.

   ![Não Azure](./media/security-center-onboarding/non-azure.png)

3. Clique em **Configurar** em **Adicionar novos computadores não Azure**. É apresentada uma lista das áreas de trabalho do Log Analytics. A lista inclui, se aplicável, a área de trabalho predefinida criada para si pelo Centro de Segurança quando o aprovisionamento automático foi ativado. Selecione esta área de trabalho ou outra área de trabalho que queira utilizar.

   ![Adicionar computador não pertencente ao Azure][7]

Se você tiver espaços de trabalho existentes, eles serão listados em **Adicionar novos computadores não Azure**. Você pode adicionar computadores a um espaço de trabalho existente ou criar um novo espaço de trabalho. Para criar um novo espaço de trabalho, selecione o link **Adicionar um novo espaço de trabalho**.

### <a name="add-new-non-azure-computers-from-compute"></a>Adicionar novos computadores não Azure da **computação**

**Criar um novo espaço de trabalho e adicionar computador**

1. Em **Adicionar novos computadores não Azure**, selecione **Adicionar um novo espaço de trabalho**.

   ![Adicionar um novo espaço de trabalho][4]

2. Em **segurança e auditoria**, selecione **espaço de trabalho do OMS** para criar um novo espaço de trabalho.
   > [!NOTE]
   > As áreas de trabalho do OMS são agora referidas como áreas de trabalho do Log Analytics.
3. Em **espaço de trabalho do OMS**, insira as informações para seu espaço de trabalho.
4. Em **espaço de trabalho do OMS**, selecione **OK**. Depois de selecionar OK, você receberá um link para baixar um agente do Windows ou Linux e chaves para a sua ID do espaço de trabalho a ser usada na configuração do agente.
5. Em **segurança e auditoria**, selecione **OK**.

**Selecionar um espaço de trabalho existente e adicionar computador**

Você pode adicionar um computador seguindo o fluxo de trabalho da **integração**, como mostrado acima. Você também pode adicionar um computador seguindo o fluxo de trabalho de **computação**. Neste exemplo, usamos **Compute**.

1. Retorne ao menu principal da central de segurança e ao painel **visão geral** .

   ![Descrição geral][5]

2. Selecione **computação & aplicativos**.
3. Em **computação & aplicativos**, selecione **Adicionar computadores**.

   ![Painel Computação][6]

4. Em **Adicionar novos computadores não Azure**, selecione um espaço de trabalho para conectar o computador ao e clique em **Adicionar computadores**.

   ![Adicionar computadores][7]

   A folha **agente direto** fornece um link para baixar um agente do Windows ou Linux, bem como a ID do espaço de trabalho e as chaves a serem usadas na configuração do agente.

## <a name="next-steps"></a>Passos seguintes
Neste artigo, você aprendeu a integrar recursos do Azure e não Azure a fim de se beneficiar da segurança avançada da central de segurança. Para fazer mais com seus recursos integrados, consulte

- [Ativar a recolha de dados](security-center-enable-data-collection.md)
- [Relatório de informações sobre ameaças](security-center-threat-report.md)
- [Acesso just-in-time à VM](security-center-just-in-time.md)

<!--Image references-->
[1]: ./media/security-center-onboarding/onboard.png
[2]: ./media/security-center-onboarding/onboard-subscription.png
[3]: ./media/security-center-onboarding/get-started.png
[4]: ./media/security-center-onboarding/create-workspace.png
[5]: ./media/security-center-onboarding/overview.png
[6]: ./media/security-center-onboarding/compute-blade.png
[7]: ./media/security-center-onboarding/add-computer.png
[8]: ./media/security-center-onboarding/onboard-workspace.png
