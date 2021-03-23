---
title: Criar relatórios de avaliação de riscos
description: Obtenha informação sobre os riscos de rede detetados por sensores individuais ou uma visão agregada dos riscos detetados por todos os sensores.
ms.date: 12/17/2020
ms.topic: how-to
ms.openlocfilehash: 853157ef1b97fefdd15785b2a71c7ccc5d06a9a9
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784259"
---
# <a name="risk-assessment-reporting"></a>Relatórios de avaliação de riscos

## <a name="about-risk-assessment-reports"></a>Sobre relatórios de avaliação de risco

Os relatórios de avaliação dos riscos fornecem:

- Uma pontuação de segurança global para os dispositivos detetados por sensores organizacionais.

- Uma pontuação de segurança para cada dispositivo de rede detetado por um sensor individual.

- Uma desagregação do número de dispositivos vulneráveis, dispositivos que precisam de ser melhorados e dispositivos seguros.

-  Informação sobre questões de segurança e operacionais:

    - Problemas de configuração

    - Vulnerabilidade do dispositivo priorizada por nível de segurança

    - Problemas de segurança da rede

    - Questões operacionais da rede

    - Ligações às redes ics

    - Conexões à Internet

    - Indicadores industriais de malware

    - Problemas de protocolo

    - Vetores de ataque

### <a name="risk-mitigation"></a>Mitigação de riscos

Os relatórios fornecem recomendações para ajudá-lo a melhorar a sua pontuação de segurança. Por exemplo, instale as mais recentes atualizações de segurança, atualize o firmware para a versão mais recente ou acompanhe os alertas.

## <a name="about-security-scores"></a>Sobre pontuações de segurança

A pontuação geral de segurança da rede é gerada em cada relatório. A pontuação representa a percentagem de 100% de segurança. Por exemplo, uma pontuação de 30% indicaria que a sua rede 30% segura.

As pontuações de Avaliação de Risco baseiam-se em informações aprendidas com a inspeção de pacotes, motores de modelação comportamental e um design de máquina estatal específico da SCADA.

**Dispositivos Seguros** são dispositivos com uma pontuação de segurança superior a 90 %.

**Dispositivos que precisam de ser melhorados**: Dispositivos com uma pontuação de segurança entre 70% e 89 %.

**Dispositivos vulneráveis** são dispositivos com uma pontuação de segurança inferior a 70 %.

## <a name="create-risk-assessment-reports"></a>Criar relatórios de avaliação de riscos

Criar um relatório de avaliação de risco PDF. O nome do relatório é gerado automaticamente como risk-assessment-report-1.pdf. O número é atualizado para cada novo relatório que criar.  A hora e o dia da criação são exibidos.

### <a name="create-a-sensor-risk-assessment-report"></a>Criar um relatório de avaliação do risco de sensores

Crie um relatório de avaliação de risco com base em deteções feitas pelo sensor em que está iniciado.

Para criar um relatório:

1. Faça login na consola do sensor.
1. Selecione **Avaliação de Risco** no menu lateral.
1. **Selecione Relatório de Geração**. O relatório aparece na secção relatórios arquivados.
1. Selecione o relatório na secção Relatórios Arquivados para o descarregar.

:::image type="content" source="media/how-to-generate-reports/risk-assessment.png" alt-text="Uma visão da avaliação de risco.":::

Para importar um logotipo da empresa:

- Selecione **o logotipo de importação**.

### <a name="create-an-on-premises-management-console-risk-assessment-report"></a>Criar um relatório de avaliação do risco de consola de gestão no local

Crie um relatório de avaliação de risco baseado em deteções feitas por qualquer um dos sensores geridos pela sua consola de gestão no local. 

Para criar um relatório:

1. Selecione **Avaliação de Risco** no menu lateral.

2. Selecione um sensor na lista de retirada do **sensor Select.**

3. **Selecione Relatório de Geração**.

4. Selecione **Baixar** na secção **Relatórios Arquivados.**

Para importar um logotipo da empresa:

- Selecione **o logotipo de importação**.

:::image type="content" source="media/how-to-generate-reports/import-logo-screenshot.png" alt-text="Importe o seu logotipo através da vista de avaliação de risco.":::

## <a name="see-also"></a>Ver também

[Relatório de vetor de ataque](how-to-create-attack-vector-reports.md)

