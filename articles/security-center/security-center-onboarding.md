---
title: Segurança reforçada de nível standard - Centro de Segurança Azure
description: " Aprenda a bordo do Azure Security Center Standard para uma maior segurança. "
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
ms.openlocfilehash: be26a9d4c66412518079de303ac0764d979c3e7c
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77912057"
---
# <a name="onboarding-to-azure-security-center-standard-for-enhanced-security"></a>Embarque para O Centro de Segurança Azure Standard para maior segurança
Atualize para o Security Center Standard para aproveitar a gestão de segurança reforçada e proteção contra ameaças para as suas cargas de trabalho híbridas em nuvem. Pode tentar sem Padrão. Consulte a [página](https://azure.microsoft.com/pricing/details/security-center/) de preços do Centro de Segurança para obter mais informações.

O nível padrão do Centro de Segurança inclui:

- **Segurança híbrida** – Obtenha uma visão unificada de segurança em todas as suas instalações e cargas de trabalho em nuvem. Aplique políticas de segurança e avalie continuamente a segurança das suas cargas de trabalho híbridas em nuvem para garantir o cumprimento das normas de segurança. Recolher, pesquisar e analisar dados de segurança de várias fontes, incluindo firewalls e outras soluções parceiras.
- **Alertas de segurança** - Utilize análises avançadas e o Microsoft Intelligent Security Graph para obter uma vantagem sobre ciberataques em evolução. Alavancar a análise comportamental incorporada e a aprendizagem automática para identificar ataques e explorações de zero dias. Monitorize redes, máquinas e serviços de nuvem para ataques e atividade pós-violação. Agilize a investigação com ferramentas interativas e inteligência de ameaça contextual.
- **Controlos** de acesso e aplicação - Bloqueie malware e outras aplicações indesejadas aplicando recomendações de whitelisting adaptadas às suas cargas de trabalho específicas e alimentadas por machine learning. Reduza a superfície de ataque da rede com acesso controlado e justo aos portos de gestão em VMs Azure, reduzindo drasticamente a exposição à força bruta e outros ataques de rede.

## <a name="detecting-unprotected-resources"></a>Deteção de recursos desprotegidos
O Centro de Segurança deteta automaticamente quaisquer subscrições ou áreas de trabalho do Azure não ativadas para o Centro de Segurança Standard. Isto inclui as subscrições do Azure que utilizam o Centro de Segurança Gratuito e as áreas de trabalho que não têm a solução de Segurança ativada.

Pode atualizar toda uma subscrição azure para o nível Standard, que é herdada por todos os recursos suportados dentro da subscrição. A aplicação do nível Standard a um espaço de trabalho aplica-se a todos os recursos que reportem ao espaço de trabalho.

> [!NOTE]
> Pode querer gerir os seus custos e limitar a quantidade de dados recolhidos para uma solução, limitando-os a um determinado conjunto de agentes. [O alvo da solução](../operations-management-suite/operations-management-suite-solution-targeting.md) permite-lhe aplicar um âmbito à solução e visar um subconjunto de computadores no espaço de trabalho. Se estiver a utilizar soluções direcionadas, o Security Center lista o espaço de trabalho como não tendo uma solução.
>
>

## <a name="upgrade-an-azure-subscription-or-workspace"></a>Atualizar uma subscrição azure ou espaço de trabalho
Para atualizar uma subscrição ou espaço de trabalho para o padrão:
1. No menu principal do Centro de Segurança, selecione **Introdução**.
  ![Introdução](./media/security-center-onboarding/get-started.png)
2. Em **Atualização**, o Centro de Segurança apresenta uma lista de subscrições e áreas de trabalho elegíveis para inclusão. 
   - Pode clicar em **Aplicar a sua versão de avaliação** para ver uma lista de todas as subscrições e áreas de trabalho com o respetivo estado de elegibilidade de avaliação.
   -    Pode atualizar as subscrições e áreas de trabalho que não são elegíveis para avaliação.
   -    Pode selecionar as áreas de trabalho e subscrições elegíveis para iniciar a avaliação.
3.  Clique em **Iniciar avaliação** para iniciar a avaliação nas subscrições selecionadas.
  ![Selecione](./media/security-center-onboarding/select-subscription.png) de subscrição


   > [!NOTE]
   > As capacidades gratuitas do Centro de Segurança são aplicadas apenas aos seus VMs E VMSS Azure. As capacidades Gratuitas não são aplicadas aos seus computadores não-Azure. Se selecionar standard, as capacidades Standard são aplicadas a todos os VMs Azure, conjuntos de escala VM e computadores não Azure reportando para o espaço de trabalho. Recomendamos que aplique a Standard para fornecer segurança avançada aos seus recursos Azure e não-Azure.
   >

## <a name="onboard-non-azure-computers"></a>Computadores não Azure a bordo
O Centro de Segurança pode monitorizar a postura de segurança dos seus computadores não Azure, mas, primeiro, tem de incluir esses recursos. Pode adicionar computadores não-Azure a partir da lâmina **iniciar** ou da lâmina **Compute.** Vamos percorrer os dois métodos.

### <a name="add-new-non-azure-computers-from-getting-started"></a>Adicione novos computadores não-Azure de **Iniciar**

1. Voltar a **Começar.**
2. Selecione o separador **Introdução**.

   ![Não-Azure](./media/security-center-onboarding/non-azure.png)

3. Clique em **Configurar** em **Adicionar novos computadores não Azure**. É apresentada uma lista das áreas de trabalho do Log Analytics. A lista inclui, se aplicável, a área de trabalho predefinida criada para si pelo Centro de Segurança quando o aprovisionamento automático foi ativado. Selecione esta área de trabalho ou outra área de trabalho que queira utilizar.

   ![Adicionar computador não pertencente ao Azure][7]

Se tiver espaços de trabalho existentes, estão listados em **adicionar novos computadores Não-Azure**. Pode adicionar computadores a um espaço de trabalho existente ou criar um novo espaço de trabalho. Para criar um novo espaço de trabalho, selecione o link **adicione um novo espaço de trabalho**.

### <a name="add-new-non-azure-computers-from-compute"></a>Adicione novos computadores não-Azure da **Compute**

**Criar um novo espaço de trabalho e adicionar computador**

1. Em **adicionar novos computadores não Azure,** selecione **adicionar um novo espaço**de trabalho .

   ![Adicione um novo espaço de trabalho][4]

2. No âmbito **da Segurança e Auditoria,** selecione **Workspace OMS** para criar um novo espaço de trabalho.
   > [!NOTE]
   > As áreas de trabalho do OMS são agora referidas como áreas de trabalho do Log Analytics.
3. No espaço de **trabalho OMS,** insira as informações para o seu espaço de trabalho.
4. Em **oms workspace,** selecione **OK**. Depois de selecionar OK, receberá um link para descarregar um agente Windows ou Linux e chaves para o seu ID do espaço de trabalho usar na configuração do agente.
5. Em **Segurança e Auditoria,** selecione **OK**.

**Selecione um espaço de trabalho existente e adicione computador**

Pode adicionar um computador seguindo o fluxo de trabalho do **Onboarding,** como mostrado acima. Também pode adicionar um computador seguindo o fluxo de trabalho da **Compute**. Neste exemplo, usamos **a Compute**.

1. Volte ao menu principal do Security Center e ao painel de visão **geral.**

   ![Descrição geral][5]

2. Selecione **Compute e apps.**
3. Em **Compute e apps,** selecione **Adicionar computadores**.

   ![Painel Computação][6]

4. Em **adicionar novos computadores não-Azure,** selecione um espaço de trabalho para ligar o computador e clique em **Adicionar Computadores**.

   ![Adicionar computadores][7]

   A lâmina **do Agente Direto** fornece um link para descarregar um agente Windows ou Linux, bem como o ID do espaço de trabalho e as chaves a utilizar na configuração do agente.

## <a name="next-steps"></a>Passos seguintes
Neste artigo aprendeu a bordo de recursos Azure e não-Azure para beneficiar da segurança avançada do Security Center. Para fazer mais com os seus recursos a bordo, veja

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
