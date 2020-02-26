---
title: Centro de Segurança do Azure e Máquinas Virtuais do Azure | Microsoft Docs
description: Este documento ajuda-o a compreender como o Centro de Segurança do Azure pode proteger as suas Máquinas Virtuais do Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 5fe5a12c-5d25-430c-9d47-df9438b1d7c5
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/24/2017
ms.author: yurid
ms.openlocfilehash: ed2c1f874bd61e846cbdf1f68181a8f374c89524
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77603372"
---
# <a name="azure-security-center-and-azure-virtual-machines"></a>Centro de Segurança do Azure e Máquinas Virtuais do Azure
O [Centro de Segurança do Azure](https://azure.microsoft.com/services/security-center/) ajuda-o a evitar, detetar e responder a ameaças. Fornece gestão de políticas e monitorização de segurança integrada nas suas subscrições do Azure, ajuda a detetar ameaças que caso contrário podem passar despercebidas e funciona com um ecossistema abrangente de soluções de segurança.

Este artigo mostra como o Centro de Segurança o pode ajudar a proteger as suas Máquinas Virtuais do Azure (VM).

## <a name="why-use-security-center"></a>Porquê utilizar o Centro de Segurança?
O Centro de Segurança ajuda-o a salvaguardar os dados da máquina virtual no Azure, oferecendo visibilidade sobre as definições de segurança da sua máquina virtual. Quando o Centro de Segurança salvaguarda as suas VMs, as seguintes funcionalidades estarão disponíveis:

* Definições de segurança do Sistema Operativo (OS) com as regras de configuração recomendadas
* Segurança do sistema e atualizações críticas em falta
* Recomendações do Endpoint protection
* Validação de encriptação do disco
* Avaliação e remediação de vulnerabilidades
* Deteção de ameaças

Além de ajudar a proteger as VMs do Azure, o Centro de Segurança também oferece monitorização de segurança e gestão de Serviços Cloud, Serviços Aplicacionais, Redes Virtuais e muito mais. 

> [!NOTE]
> Consulte [Introduction to Azure Security Center (Introdução ao Centro de Segurança do Azure)](security-center-intro.md) para saber mais sobre o Centro de Segurança do Azure.
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
Para começar a utilizar o Centro de Segurança do Azure, terá de conhecer e considerar o seguinte:

* Precisa de uma subscrição do Microsoft Azure. Consulte [Security Center Pricing (Preços do Centro de Segurança)](https://azure.microsoft.com/pricing/details/security-center/), para obter mais informações sobre as camadas gratuitas e standard do Centro de Segurança.
* Planeie a adoção do Centro de Segurança, consulte [Azure Security Center planning and operations guide (Guia de planeamento e funcionamento do Centro de Segurança do Azure)](security-center-planning-and-operations-guide.md), para saber mais sobre as considerações de planeamento e funcionamento.
* Para obter informações sobre sistemas operativos suportados, consulte [o Azure Security Center frequentemente feito perguntas (FAQ)](faq-vms.md). 

## <a name="set-security-policy"></a>Definir política de segurança
A recolha de dados tem de ser ativada para que o Centro de Segurança do Azure possa recolher as informações que necessita para fornecer recomendações e alertas que são gerados com base na política de segurança configurada por si. Na imagem abaixo, pode ver que a **Recolha de dados** foi **Ativada**.

Uma política de segurança define o conjunto de controlos que são recomendados para recursos dentro da subscrição especificada ou do grupo de recursos. Antes de ativar a política de segurança, tem de ter a recolha de dados ativada, o Centro de Segurança recolhe dados de máquinas virtuais para avaliar o respetivo estado de segurança, fornecer recomendações de segurança e alertá-lo sobre ameaças. No Centro de Segurança, é possível definir políticas para as suas subscrições ou grupos de recursos do Azure, de acordo com as necessidades de segurança da empresa e do tipo de aplicações ou sensibilidade dos dados em cada subscrição. 

![Política de segurança](./media/security-center-virtual-machine/security-center-virtual-machine-fig1.png)

> [!NOTE]
> Para saber mais sobre cada **Política de prevenção** disponível, consulte o artigo [Set security policies (Definir políticas de segurança)](tutorial-security-policy.md).

## <a name="manage-security-recommendations"></a>Gerir recomendações de segurança
O Centro de Segurança analisa o estado de segurança dos seus recursos do Azure. Quando o Centro de Segurança identifica potenciais vulnerabilidades de segurança, cria recomendações. As recomendações orientam-no no processo de configuração de controlos necessários.

Após definir uma política de segurança, o Centro de Segurança analisa o estado de segurança dos seus recursos para identificar potenciais vulnerabilidades. As recomendações são apresentadas num formato de tabela em que cada linha representa uma recomendação específica. A tabela abaixo fornece alguns exemplos de recomendações para VMs do Azure e o que cada um irá fazer se as aplicar. Ao selecionar uma recomendação, ser-lhe-ão fornecidas informações que lhe mostram como implementar a recomendação no Centro de Segurança.

| Recomendação | Descrição |
| --- | --- |
| Ativar a recolha de dados de subscrições |Recomenda-se que ative a recolha de dados na política de segurança para cada uma das suas subscrições e todas as máquinas virtuais (VMs) nas suas subscrições. |
| Remediar vulnerabilidades do SO |Recomenda-se que alinhe as configurações do SO com as regras de configuração recomendadas, por exemplo, não permitir que as palavras-passe sejam guardadas. |
| Aplicar atualizações do Sistema |Recomenda-se que implemente o sistema de segurança em falta e atualizações críticas nas VMs. |
| Reiniciar após atualizações do sistema |Recomenda-se que reinicie uma VM para concluir o processo de aplicar atualizações do sistema. |
| Instalar o Endpoint Protection |Recomenda-se que aprovisione programas de antimalware em VMs (apenas VMs do Windows). |
| Ativar o Agente de VM |Permite-lhe ver as VMs que necessitam do Agente de VM. O Agente de VM tem de estar instalado em VMs para aprovisionar a análise de patch, a análise de linha de base e os programas de antimalware. O Agente de VM é instalado por predefinição em VMs que são implementadas a partir do Azure Marketplace. O artigo [VM Agent and Extensions – Part 2 (Agente de VM e Extensões – Parte 2)](https://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) fornece informações sobre como instalar o Agente de VM. |
| Aplicar encriptação de discos |Recomenda-se que encripte os discos da VM com o Azure Disk Encryption (VMs Windows e Linux). A encriptação é recomendada para o SO e os volumes de dados na sua VM. |
| Avaliação de vulnerabilidades não instalada |Recomenda-se de que instala uma solução de avaliação de vulnerabilidades na sua VM. |
| Remediar vulnerabilidades |Permite-lhe ver as vulnerabilidades de sistema e de aplicações detetadas pela solução de avaliação de vulnerabilidade instalada na sua VM. |

> [!NOTE]
> Para saber mais sobre as recomendações, consulte o artigo [Managing security recommendations (Gerir recomendações de segurança)](security-center-recommendations.md).
> 
> 

## <a name="monitor-security-health"></a>Monitorizar o estado de funcionamento da segurança
Depois de ativar [políticas de segurança](tutorial-security-policy.md) para os recursos de uma subscrição, o Centro de Segurança irá analisar a segurança dos seus recursos para identificar potenciais vulnerabilidades.  Pode ver o estado de segurança dos seus recursos, juntamente com quaisquer problemas no painel **Estado de funcionamento de segurança de recursos**. Quando clica em **Máquinas virtuais** no mosaico de estado de funcionamento **Segurança de recursos**, o painel **Máquinas virtuais** será aberto com recomendações para as VMs. 

![Estado de funcionamento da segurança](./media/security-center-virtual-machine/security-center-virtual-machine-fig2.png)

## <a name="manage-and-respond-to-security-alerts"></a>Gerir e responder a alertas de segurança
O Centro de Segurança recolhe, analisa e integra automaticamente dados de registo a partir dos seus recursos do Azure, da rede e soluções de parceiros ligadas, (como soluções de proteção de ponto final e firewall), para detetar ameaças reais e reduzir os falsos positivos. Ao tirar partido de uma agregação diversa de [capacidades de deteção](security-center-alerts-overview.md#detect-threats), o Centro de Segurança pode gerar alertas de segurança prioritários para o ajudar a investigar rapidamente o problema e fornecer recomendações para saber como corrigir possíveis ataques.

![Alertas de segurança](./media/security-center-virtual-machine/security-center-virtual-machine-fig3.png)

Selecione um alerta de segurança para obter mais informações sobre o(s) evento(s) que acionaram o alerta e quais os passos (se existirem) necessários para remediar um ataque. Os alertas de segurança estão agrupados por tipo e data.

## <a name="see-also"></a>Consulte também
Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Definir políticas de segurança no Centro de Segurança do Azure](tutorial-security-policy.md) – Saiba como configurar políticas de segurança para as suas subscrições e grupos de recursos do Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
* [FAQ - Máquinas Virtuais](faq-vms.md) -- Encontre perguntas frequentes sobre a utilização do serviço.