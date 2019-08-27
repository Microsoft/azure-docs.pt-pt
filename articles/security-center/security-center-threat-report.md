---
title: Relatórios de informações sobre Ameaças do Centro de Segurança do Azure| Microsoft Docs
description: Este documento ajuda-o a utilizar os Relatórios de Informações Sobre Ameaças do Centro de Segurança do Azure durante uma investigação para obter mais informações sobre um alerta de segurança.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 5662e312-e8c2-4736-974e-576eeb333484
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2018
ms.author: v-mohabe
ms.openlocfilehash: 7ab42fdb03209173c61b70fdf498aa77b39a1c23
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/26/2019
ms.locfileid: "70036392"
---
# <a name="azure-security-center-threat-intelligence-report"></a>Relatório de Informações Sobre Ameaças do Centro de Segurança do Azure
Este documento explica como os Relatórios de Informações Sobre Ameaças do Centro de Segurança do Azure podem ajudá-lo a obter mais informações sobre uma ameaça que gerou um alerta de segurança.

## <a name="what-is-a-threat-intelligence-report"></a>O que é um relatório de informações sobre ameaças?
A deteção de ameaças do Centro de Segurança funciona através da monitorização de informações de segurança a partir dos seus recursos do Azure, da rede e das soluções de parceiros ligadas. Analisa esta informação, muitas vezes correlacionando informações de várias origens, para identificar ameaças. Para obter mais informações, consulte [como a central de segurança do Azure detecta e responde às ameaças](security-center-alerts-overview.md#detect-threats).

Quando o Centro de Segurança identifica uma ameaça, irá acionar um [alerta de segurança](security-center-managing-and-responding-alerts.md) com informações detalhadas sobre um determinado evento, incluindo sugestões de remediação. Para auxiliar as equipes de resposta a incidentes, investigar e corrigir ameaças, a central de segurança inclui um relatório de inteligência contra ameaças que contém informações sobre a ameaça detectada, incluindo informações como:

* A identidade do atacante ou associações (se esta informação estiver disponível)
* Os objetivos dos atacantes
* As campanhas de ataque atuais e históricas (se esta informação estiver disponível)
* Táticas, ferramentas e procedimentos dos invasores
* Os indicadores de comprometimento (IoC) associados, como URLs e hashes de ficheiros
* A vitimologia, a prevalência do setor e geográfica para ajudá-lo a determinar se os recursos do Azure estão em risco
* Informações de mitigação e remediação

> [!NOTE]
> A quantidade de informações em qualquer relatório específico irá variar; o nível de detalhe baseia-se na atividade e na prevalência de software maligno.
>
>

O Centro de Segurança tem três tipos de relatórios de ameaças, que podem variar de acordo com o ataque. Os relatórios disponíveis são:

* **Relatório do Grupo de Atividade**: fornece informações detalhadas sobre os atacantes, os seus objetivos e táticas.
* **Relatório da Campanha**: centra-se nos detalhes sobre campanhas de ataque específicas.
* **Relatório de Resumo da Ameaça**: abrange todos os itens nos dois relatórios anteriores.

Esse tipo de informação é útil durante o processo de resposta a incidentes, em que há uma investigação contínua para entender a origem do ataque, as motivações do invasor e o que fazer para atenuar esse problema avançando.

## <a name="how-to-access-the-threat-intelligence-report"></a>Como aceder ao relatório de informações sobre ameaças?
Pode rever os alertas atuais ao observar o mosaico **Alertas de segurança**. Abra o portal do Azure e siga as etapas abaixo para ver mais detalhes sobre cada alerta:

1. No dashboard do Centro de Segurança, verá o mosaico **Alertas de segurança**.
2. Clique no mosaico para abrir o painel **Alertas de segurança** que contém mais detalhes sobre os alertas e clique no alerta de segurança sobre o qual quer obter mais informações.

    ![Alertas de segurança](./media/security-center-threat-report/security-center-threat-report-fig1.png)
3. Nesse caso, a folha **processo suspeito executado** mostra os detalhes sobre o alerta, conforme mostrado na figura abaixo:

    ![Detalhes do alerta de segurança](./media/security-center-threat-report/security-center-threat-report-fig2.png)
4. A quantidade de informação disponível para cada alerta de segurança irá variar de acordo com o tipo de alerta. No campo **relatórios** , você tem um link para o relatório de inteligência contra ameaças. Clique na mesma e será apresentada outra janela do browser com o ficheiro PDF.

   ![Seleção de armazenamento](./media/security-center-threat-report/security-center-threat-report-fig3.png)

A partir daqui, pode transferir o PDF para este relatório e ler mais informações sobre o problema de segurança detetado e efetuar ações com base nas informações fornecidas.

## <a name="see-also"></a>Consulte também
Neste documento, aprendeu como os Relatórios de Informações Sobre Ameaças do Centro de Segurança do Azure podem ajudar durante uma investigação sobre alertas de segurança. Para saber mais acerca do Centro de Segurança do Azure, consulte o seguinte:

* [Centro de Segurança do Azure FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md). Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Guia de operações e planeamento do Centro de Segurança do Azure](security-center-planning-and-operations-guide.md). Saiba como planear e compreender as considerações de conceção para adoção do Centro de Segurança do Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md). Saiba como gerir e responder a alertas de segurança.
* [Lidar com Incidentes de Segurança no Centro de Segurança do Azure](security-center-incident.md)
* [Blogue de Segurança do Azure](https://blogs.msdn.com/b/azuresecurity/). Encontre mensagens do blogue acerca da segurança e conformidade do Azure.
