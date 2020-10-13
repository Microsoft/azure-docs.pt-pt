---
title: Tutorial - Use O Centro de Segurança Azure para VMs windows em Azure
description: Neste tutorial, vai aprender sobre as funcionalidades do Centro de Segurança do Azure para ajudar a proteger as suas máquinas virtuais do Windows no Azure.
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 12/05/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 9eed237b1dd4d6bf6f2903b91c80847d615fe7a1
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91978293"
---
# <a name="tutorial-use-azure-security-center-to-monitor-windows-virtual-machines"></a>Tutorial: Utilizar o Centro de Segurança do Azure para monitorizar as máquinas virtuais do Windows

O Centro de Segurança do Azure pode ajudá-lo a obter visibilidade para as práticas de segurança dos seus recursos do Azure. O Centro de Segurança oferece monitorização de segurança integrada. Pode detetar ameaças que, caso contrário, poderiam passar despercebidas. Neste tutorial, pode obter informações sobre o Centro de Segurança do Azure e como:

> [!div class="checklist"]
> * Configurar a recolha de dados
> * Configurar políticas de segurança
> * Ver e corrigir problemas de estado de funcionamento da configuração
> * Rever as ameaças detetadas

## <a name="security-center-overview"></a>Descrição geral do Centro de Segurança

O Centro de Segurança identifica potenciais problemas de configuração de máquina virtual (VM) e ameaças de segurança direcionadas. Estes podem incluir as VMs em que faltam grupos de segurança de rede, discos não encriptados e ataques de protocolo RDP (Remote Desktop Protocol) de força bruta. As informações são mostradas no dashboard do Centro de Segurança, em gráficos fáceis de ler.

Para aceder ao dashboard do Centro de Segurança, no portal do Azure, no menu, selecione **Centro de Segurança**. No dashboard, pode ver o estado de funcionamento da segurança do seu ambiente do Azure, encontrar uma contagem de recomendações atuais e ver o estado atual de alertas de ameaça. Pode expandir cada gráfico de alto nível para ver mais detalhes.

![Dashboard Centro de Segurança](./media/tutorial-azure-security/asc-dash.png)

O Centro de Segurança vai mais além da deteção de dados para fornecer recomendações para problemas que deteta. Por exemplo, se uma VM tiver sido implementada sem um grupo de segurança de rede ligado à mesma, o Centro de Segurança apresenta uma recomendação, com passos de remediação que pode efetuar. Obtém a remediação automática sem sair do contexto do Centro de Segurança.  

![O Screenshot mostra a página de recomendações, que inclui uma descrição, recurso, estado e gravidade das recomendações.](./media/tutorial-azure-security/recommendations.png)

## <a name="set-up-data-collection"></a>Configurar a recolha de dados

Antes de poder obter visibilidade das configurações de segurança de VM, terá de configurar a recolha de dados do Centro de Segurança. Isto implica ativar a recolha de dados, que instala automaticamente o MMA em todas as VMs na subscrição.

1. No dashboard Centro de Segurança, clique em **Política de Segurança** e, em seguida, selecione a sua subscrição. 
2. Em **Recolha de Dados**, em **Aprovisionamento Automático** selecione **Ativado**.
3. Em **Configuração de área de trabalho predefinida**, deixe como **Utilizar as áreas de trabalho criadas pelo Centro de Segurança (predefinição)**.
4. Em **Eventos de Segurança**, mantenha a opção predefinida **Comuns**.
4. Clique em **Guardar** no início da página. 

O agente de recolha de dados do Centro de Segurança é então instalado em todas as VMs, e a recolha de dados começa. 

## <a name="set-up-a-security-policy"></a>Configurar uma política de segurança

As políticas de segurança são utilizadas para definir os itens para os quais o Centro de Segurança recolhe dados e faz recomendações. Pode aplicar políticas de segurança diferentes a diferentes conjuntos de recursos do Azure. Embora, por predefinição, os recursos do Azure sejam avaliados em relação a todos os itens de política, pode desativar os itens de política individuais para todos os recursos do Azure ou para um grupo de recursos. Para obter informações aprofundadas sobre as políticas de segurança do Centro de Segurança, veja [Definir políticas de segurança no Centro de Segurança do Azure](../../security-center/tutorial-security-policy.md). 

Para configurar uma política de segurança para toda a subscrição:

1. No dashboard Centro de Segurança, selecione **Política de Segurança** e, em seguida, selecione a sua subscrição.
2. No painel **Política de Segurança**, selecione **Política de segurança**. 
3. Sobre a **política de segurança - Lâmina de política de** segurança, ligue ou desligue os itens de política que pretende aplicar à subscrição.
4. Quando tiver terminado de selecionar as definições, selecione **Guardar** na parte superior do painel. 


![Política exclusiva](./media/tutorial-azure-security/unique-policy.png)

## <a name="view-vm-configuration-health"></a>Ver o estado de funcionamento de configuração de VM

Depois de ter ativado a recolha de dados e definido uma política de segurança, o Centro de Segurança começa a disponibilizar recomendações e alertas. À medida que as VMs são implementadas, o agente de recolha de dados é instalado. O Centro de Segurança é então preenchido com os dados das novas VMs. Para obter informações aprofundadas sobre o estado de funcionamento de configuração de VM, veja [Proteger as suas VMs no Centro de Segurança](../../security-center/asset-inventory.md). 

À medida que os dados são recolhidos, o estado de funcionamento de recursos de cada VM e os recursos do Azure relacionados são agregados. As informações são mostradas num gráfico de fácil leitura. 

Para ver o estados de funcionamento dos recursos:

1.  No dashboard do Centro de Segurança, em **Prevenção**, selecione **Computação**. 
2.  No painel **Computação**, selecione **VMs e computadores**. Esta vista fornece um resumo do estado de configuração de todas as suas VMs.

![Estado de funcionamento da computação](./media/tutorial-azure-security/compute-health.png)

Para ver todas as recomendações para uma VM, selecione a VM. As recomendações e a remediação são abordadas mais detalhadamente na secção seguinte deste tutorial.

## <a name="remediate-configuration-issues"></a>Remediar problemas de configuração

Depois de o Centro de Segurança começar a ser preenchido com dados de configuração, as recomendações são efetuadas com base na política de segurança que configurou. Por exemplo, se uma VM tiver sido configurada sem um grupo de segurança de rede associado, é feita uma recomendação para criar um. 

Para ver uma lista de todas as recomendações: 

1. No dashboard Centro de Segurança, selecione **Recomendações**.
2. Selecione uma recomendação específica. É apresentada uma lista de todos os recursos aos quais se aplica a recomendação.
3. Para aplicar uma recomendação, selecione o recurso. 
4. Siga as instruções dos passos de remediação. 

Em muitos casos, o Centro de Segurança fornece passos acionáveis que pode seguir para resolver uma recomendação sem sair do Centro de Segurança. No exemplo seguinte, o Centro de Segurança deteta um grupo de segurança de rede que tem uma regra de entrada sem restrições. Na página de recomendações, pode selecionar o botão **Editar regras de entrada**. A IU que é necessária para modificar a regra é apresentada. 

![Recomendações](./media/tutorial-azure-security/remediation.png)

À medida que as recomendações são resolvidas, são marcadas como resolvidas. 

## <a name="view-detected-threats"></a>Ver as ameaças detetadas

Além das recomendações de configuração de recursos, o Centro de Segurança apresenta os alertas de deteção de ameaças. A funcionalidade de alertas de segurança agrega os dados recolhidos de cada VM, os registos de rede do Azure e as soluções de parceiros ligadas para detetar ameaças de segurança relativamente aos recursos do Azure. Para obter informações aprofundadas sobre as capacidades de deteção de ameaças do Security Center, veja [como é que o Centro de Segurança deteta ameaças?](../../security-center/security-center-alerts-overview.md#detect-threats)

A funcionalidade de alertas de segurança requer que o escalão de preço do Centro de Segurança seja aumentado de *Gratuito* para *Standard*. Um **teste gratuito** está disponível quando se muda para este nível de preços mais elevado. 

Para alterar o escalão de preço:  

1. No dashboard Centro de Segurança, clique em **Política de Segurança** e, em seguida, selecione a sua subscrição.
2. **Selecione o nível de preços**.
3. Selecione **Standard** e, em seguida, clique em **Guardar** na parte superior do painel.


Depois de alterar o escalão de preço, o gráfico de alertas de segurança começa a ser preenchido, à medida que são detetadas ameaças de segurança.

![Alertas de segurança](./media/tutorial-azure-security/security-alerts.png)

Selecione um alerta para ver as informações. Por exemplo, pode ver uma descrição da ameaça, a hora da deteção, todas as tentativas de ameaça e a remediação recomendada. No exemplo seguinte, foi detetado um ataque RDP de força bruta, com 294 tentativas falhadas de RDP. É fornecida uma resolução recomendada.

![Ataque RDP](./media/tutorial-azure-security/rdp-attack.png)

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, configurou o Centro de Segurança do Azure e, em seguida, reviu VMs no Centro de Segurança. Aprendeu a:

> [!div class="checklist"]
> * Configurar a recolha de dados
> * Configurar políticas de segurança
> * Ver e corrigir problemas de estado de funcionamento da configuração
> * Rever as ameaças detetadas

Avance para o próximo tutorial para saber como instalar uma pilha .NET SQL&#92;IIS&#92; num par de VMs do Windows.

> [!div class="nextstepaction"]
> [Pilha .NET SQL&#92;IIS&#92;.](tutorial-iis-sql.md)