---
title: Relatórios de informações sobre Ameaças do Centro de Segurança do Azure| Microsoft Docs
description: Este documento ajuda-o a utilizar os Relatórios de Informações Sobre Ameaças do Centro de Segurança do Azure durante uma investigação para obter mais informações sobre um alerta de segurança.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 5662e312-e8c2-4736-974e-576eeb333484
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2018
ms.author: memildin
ms.openlocfilehash: f8b4063d87fa9a89dccd42eddea644609bd6ff27
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77921254"
---
# <a name="azure-security-center-threat-intelligence-report"></a>Relatório de Informações Sobre Ameaças do Centro de Segurança do Azure
Este documento explica como os Relatórios de Informações Sobre Ameaças do Centro de Segurança do Azure podem ajudá-lo a obter mais informações sobre uma ameaça que gerou um alerta de segurança.

## <a name="what-is-a-threat-intelligence-report"></a>O que é um relatório de informações sobre ameaças?
A proteção contra ameaças do Security Center funciona através da monitorização de informações de segurança dos seus recursos Azure, da rede e soluções de parceiros conectados. Analisa esta informação, muitas vezes correlacionando informações de várias origens, para identificar ameaças. Para mais informações, consulte [como o Azure Security Center deteta e responde a ameaças](security-center-alerts-overview.md#detect-threats).

Quando o Centro de Segurança identifica uma ameaça, irá acionar um [alerta de segurança](security-center-managing-and-responding-alerts.md) com informações detalhadas sobre um determinado evento, incluindo sugestões de remediação. Para ajudar as equipas de resposta a incidentes, investigar e remediar ameaças, o Centro de Segurança inclui um relatório de inteligência de ameaças que contém informações sobre a ameaça que foi detetada, incluindo informações como:

* A identidade do atacante ou associações (se esta informação estiver disponível)
* Os objetivos dos atacantes
* As campanhas de ataque atuais e históricas (se esta informação estiver disponível)
* Táticas, ferramentas e procedimentos dos agressores
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

Este tipo de informação é útil durante o processo de resposta ao incidente, onde está em curso uma investigação para compreender a origem do ataque, as motivações do agressor e o que fazer para mitigar esta questão avançando.

## <a name="how-to-access-the-threat-intelligence-report"></a>Como aceder ao relatório de informações sobre ameaças?
Pode rever os alertas atuais ao observar o mosaico **Alertas de segurança**. Abra o portal Azure e siga os passos abaixo para ver mais detalhes sobre cada alerta:

1. No dashboard do Centro de Segurança, verá o mosaico **Alertas de segurança**.
2. Clique no mosaico para abrir o painel **Alertas de segurança** que contém mais detalhes sobre os alertas e clique no alerta de segurança sobre o qual quer obter mais informações.

    ![Alertas de segurança](./media/security-center-threat-report/security-center-threat-report-fig1.png)
3. Neste caso, a lâmina executada pelo **processo suspeito** mostra os detalhes sobre o alerta como mostrado na figura abaixo:

    ![Detalhes do alerta de segurança](./media/security-center-threat-report/security-center-threat-report-fig2.png)
4. A quantidade de informação disponível para cada alerta de segurança irá variar de acordo com o tipo de alerta. No campo reporte de **relatórios** de ameaças, tem uma ligação com o relatório da inteligência da ameaça. Clique na mesma e será apresentada outra janela do browser com o ficheiro PDF.

   ![Seleção de armazenamento](./media/security-center-threat-report/security-center-threat-report-fig3.png)

A partir daqui, pode transferir o PDF para este relatório e ler mais informações sobre o problema de segurança detetado e efetuar ações com base nas informações fornecidas.

## <a name="see-also"></a>Consulte também
Neste documento, aprendeu como os Relatórios de Informações Sobre Ameaças do Centro de Segurança do Azure podem ajudar durante uma investigação sobre alertas de segurança. Para saber mais acerca do Centro de Segurança do Azure, consulte o seguinte:

* Guia de [planeamento e operações do Azure Security Center.](security-center-planning-and-operations-guide.md) Saiba como planear e compreender as considerações de conceção para adoção do Centro de Segurança do Azure.
* [Gestão e resposta a alertas](security-center-managing-and-responding-alerts.md)de segurança no Centro de Segurança Azure. Saiba como gerir e responder a alertas de segurança.
* [Lidar com Incidentes de Segurança no Centro de Segurança do Azure](security-center-incident.md)