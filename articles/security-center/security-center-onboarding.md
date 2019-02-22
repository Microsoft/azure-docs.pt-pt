---
title: A adesão ao Azure Security Center Standard para segurança avançada | Documentos da Microsoft
description: " Saiba como para carregar para o Azure Security Center Standard for enhanced security. "
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 19/02/2019
ms.author: monhaber
ms.openlocfilehash: ab99116cabc14c70698a962c803adb87394e1796
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56651109"
---
# <a name="onboarding-to-azure-security-center-standard-for-enhanced-security"></a>Adesão ao Azure Security Center Standard para uma maior segurança
Atualizar para o Centro de segurança Standard para tirar partido da gestão de segurança avançada e proteção contra ameaças para as cargas de trabalho de cloud híbrida.  Pode tentar Standard gratuita. Consulte o Centro de segurança [página de preços](https://azure.microsoft.com/pricing/details/security-center/) para obter mais informações.

Centro de segurança Standard inclui:

- **Segurança híbrida** – obtenha uma visão unificada de segurança em todos os no local e na cloud cargas de trabalho. Aplicar políticas de segurança e avaliar continuamente a segurança das suas cargas de trabalho de cloud híbrida para garantir a conformidade com normas de segurança. Recolha, pesquise e analise os dados de segurança de várias origens, incluindo firewalls e outras soluções de parceiros.
- **Deteção de ameaças avançadas** -utilize análises avançadas e o Microsoft Intelligent Security Graph para obter informações sobre a evolução de ataques informáticos.  Tire partido da análise comportamental e do machine learning incorporados para identificar ataques e explorações de dia zero. Monitorize redes, máquinas e serviços cloud para ataques recebidos e atividade pós-falhas. Uniformize a investigação com ferramentas interativas e informações sobre ameaças contextuais.
- **Controlos de acesso e aplicação** -bloqueie o malware e outras aplicações indesejadas ao aplicar recomendações de lista aprovada adaptadas às suas cargas de trabalho específicas e com tecnologia de machine Learning. Reduza a superfície de ataque rede com acesso just-in-time e controlado às portas de gestão em VMs do Azure, reduzindo drasticamente a exposição a ataques de força bruta e outros ataques de rede.

## <a name="detecting-unprotected-resources"></a>Detetar recursos desprotegidos     
O Centro de Segurança deteta automaticamente quaisquer subscrições ou áreas de trabalho do Azure não ativadas para o Centro de Segurança Standard. Isto inclui as subscrições do Azure que utilizam o Centro de Segurança Gratuito e as áreas de trabalho que não têm a solução de Segurança ativada.

Pode atualizar uma subscrição do Azure completa para o escalão Standard, que é herdado por todos os recursos dentro da subscrição, ou pode definir uma política exclusiva para atualizar apenas um grupo de recursos específico. Se as definições de política de grupo de recursos são exclusivas, o Centro de segurança não irá substituir as políticas de preços ao atualizar a subscrição para o escalão Standard. Aplicar o padrão de camada para uma subscrição se aplica a todos os recursos suportados na subscrição. Aplicar o padrão de camada para uma área de trabalho aplica-se a todos os recursos de relatórios para a área de trabalho.

> [!NOTE]
> Poderá querer gerir os custos e limitar a quantidade de dados recolhidos para uma solução ao limitá-la para um determinado conjunto de agentes. [Filtragem da solução](../operations-management-suite/operations-management-suite-solution-targeting.md) permite-lhe aplicar um âmbito para a solução e um subconjunto de computadores na área de trabalho de destino.  Se estiver a utilizar o destino de solução, o Centro de segurança apresenta uma lista de área de trabalho como não tendo uma solução.
>
>

## <a name="upgrade-an-azure-subscription-or-workspace"></a>Atualizar uma subscrição do Azure ou a área de trabalho
Para atualizar uma subscrição ou a área de trabalho padrão:
1. No menu principal do Centro de Segurança, selecione **Introdução**.
  ![Introdução](./media/security-center-onboarding/get-started.png)
2. Em **Atualização**, o Centro de Segurança apresenta uma lista de subscrições e áreas de trabalho elegíveis para inclusão. 
   - Pode clicar em **Aplicar a sua versão de avaliação** para ver uma lista de todas as subscrições e áreas de trabalho com o respetivo estado de elegibilidade de avaliação.
   -    Pode atualizar as subscrições e áreas de trabalho que não são elegíveis para avaliação.
   -    Pode selecionar as áreas de trabalho e subscrições elegíveis para iniciar a avaliação.
3.  Clique em **Iniciar avaliação** para iniciar a avaliação nas subscrições selecionadas.
  ![Selecione a subscrição](./media/security-center-onboarding/select-subscription.png)


   > [!NOTE]
   > Funções gratuitas do Centro de segurança são aplicadas às suas VMs do Azure e o VMSS apenas. Os recursos gratuitos não são aplicados aos seus computadores não Azure. Se selecionar padrão, os recursos padrão são aplicados a todas as VMs do Azure, conjuntos de dimensionamento de VMS e computadores não Azure relatórios para a área de trabalho. Recomendamos que aplique padrão para fornecer segurança avançada para o Azure e os recursos não Azure.
   >
   >

## <a name="onboard-non-azure-computers"></a>Inclusão de computadores não pertencentes ao Azure
O Centro de Segurança pode monitorizar a postura de segurança dos seus computadores não Azure, mas, primeiro, tem de incluir esses recursos. Pode adicionar computadores não pertencentes ao Azure a partir da **introdução ao** painel ou a partir do **computação** painel. Vamos explicar através de ambos os métodos.

### <a name="add-new-non-azure-computers-from-getting-started"></a>Adicionar novos computadores não pertencentes ao Azure a partir de **introdução**

1. Volte ao **introdução ao**.   
2. Selecione o separador **Introdução**.

  ![Não Pertencente ao Azure](./media/security-center-onboarding/non-azure.png)

3. Clique em **Configurar** em **Adicionar novos computadores não Azure**. É apresentada uma lista das áreas de trabalho do Log Analytics. A lista inclui, se aplicável, a área de trabalho predefinida criada para si pelo Centro de Segurança quando o aprovisionamento automático foi ativado. Selecione esta área de trabalho ou outra área de trabalho que queira utilizar.

  ![Adicionar computador não pertencente ao Azure][7]

Se tiver áreas de trabalho existentes, estão listados na **adicionar novos computadores não pertencentes ao Azure**. Pode adicionar computadores a uma área de trabalho existente ou criar uma nova área de trabalho. Para criar uma nova área de trabalho, selecione a ligação **adicionar uma nova área de trabalho**.

### <a name="add-new-non-azure-computers-from-compute"></a>Adicionar novos computadores não pertencentes ao Azure a partir de **de computação**

**Criar uma nova área de trabalho e adicionar o computador**

1. Sob **adicionar novos computadores não Azure**, selecione **adicionar uma nova área de trabalho**.

   ![Adicionar uma nova área de trabalho][4]

2. Sob **segurança e auditoria**, selecione **área de trabalho OMS** para criar uma nova área de trabalho.
> [!NOTE]
> As áreas de trabalho do OMS são agora referidas como áreas de trabalho do Log Analytics.
3. Sob **área de trabalho OMS**, insira as informações da sua área de trabalho.
4. Sob **área de trabalho do OMS**, selecione **OK**.  Depois de selecionar ' OK ', irá obter uma ligação para transferir um agente do Windows ou Linux e as chaves para o seu ID de área de trabalho utilizar na configuração do agente.
5. Sob **segurança e auditoria**, selecione **OK**.

**Selecione uma área de trabalho existente e adicionar o computador**

Pode adicionar um computador, seguindo o fluxo de trabalho do **inclusão**, conforme mostrado acima. Também pode adicionar um computador, seguindo o fluxo de trabalho do **computação**. Neste exemplo, utilizamos **computação**.

1. Regresse ao menu principal do Centro de segurança e **descrição geral** dashboard.

   ![Descrição geral][5]

2. Selecione **computação e aplicações**.
3. Sob **computação e aplicações**, selecione **adicionar computadores**.

   ![Painel Computação][6]

4. Sob **adicionar novos computadores não Azure**, selecione uma área de trabalho para ligar o seu computador para e clique em **adicionar computadores**.

   ![Adicionar computadores][7]

 O **agente direto** painel fornece um link para baixar um agente do Windows ou Linux, bem como o ID de área de trabalho e as chaves a utilizar na configuração do agente.   

## <a name="next-steps"></a>Passos Seguintes
Neste artigo, aprendeu a carregar Azure e de recursos não Azure para se beneficiar das segurança avançada do Centro de segurança.  Para fazer mais com os recursos de integração, consulte

- [Ativar a recolha de dados](security-center-enable-data-collection.md)
- [Relatório de informações sobre ameaças](security-center-threat-report.md)
- [Just-in-acesso à VM do tempo](security-center-just-in-time.md)

<!--Image references-->
[1]: ./media/security-center-onboarding/onboard.png
[2]: ./media/security-center-onboarding/onboard-subscription.png
[3]: ./media/security-center-onboarding/get-started.png
[4]: ./media/security-center-onboarding/create-workspace.png
[5]: ./media/security-center-onboarding/overview.png
[6]: ./media/security-center-onboarding/compute-blade.png
[7]: ./media/security-center-onboarding/add-computer.png
[8]: ./media/security-center-onboarding/onboard-workspace.png
