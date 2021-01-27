---
title: Use o Centro de Segurança Azure para endurecer os seus anfitriões Docker e proteger os contentores
description: Como proteger os anfitriões do Docker e verificar se estão em conformidade com o benchmark do CIS Docker
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: b30e08a2739000d2a7ec14a95742f2654e1d2ea1
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98916239"
---
# <a name="harden-your-docker-hosts"></a>Proteger os anfitriões do Docker

O Azure Security Center identifica contentores não geridos alojados em IaaS Linux VMs, ou outras máquinas Linux que executam contentores Docker. O Centro de Segurança avalia continuamente as configurações destes contentores. Em seguida, compara-os com o [Center for Internet Security (CIS) Docker Benchmark](https://www.cisecurity.org/benchmark/docker/).

O Centro de Segurança inclui todo o conjunto de regras do CIS Docker Benchmark e alerta-o se os seus contentores não satisfizerem nenhum dos controlos. Quando encontra configurações erradas, o Centro de Segurança gera recomendações de segurança. Use a página de **recomendações** do Centro de Segurança para ver recomendações e remediar problemas.

Quando as vulnerabilidades são encontradas, são agrupadas dentro de uma única recomendação.

>[!NOTE]
> Estes controlos de referência do CIS não serão executados em instâncias geridas pela AKS ou em VMs geridos por Databricks.

## <a name="availability"></a>Disponibilidade

|Aspeto|Detalhes|
|----|:----|
|Estado de libertação:|Disponibilidade Geral (GA)|
|Preços:|Requer [Azure Defender para servidores](defender-for-servers-introduction.md)|
|Funções e permissões necessárias:|**Leitor** no espaço de trabalho ao qual o anfitrião se conecta|
|Nuvens:|![Yes](./media/icons/yes-icon.png) Nuvens comerciais<br>![Yes](./media/icons/yes-icon.png) Nacional/Soberano (Gov dos EUA, China Gov, Outro Gov)|
|||

## <a name="identify-and-remediate-security-vulnerabilities-in-your-docker-configuration"></a>Identifique e remedia vulnerabilidades de segurança na sua configuração Docker

1. A partir do menu do Centro de Segurança, abra a página **recomendações.**

1. Filtrar para a recomendação **As vulnerabilidades nas configurações de segurança do contentor devem ser remediadas** e selecionar a recomendação.

    A página de recomendação mostra os recursos afetados (anfitriões Docker). 

    :::image type="content" source="./media/monitor-container-security/docker-host-vulnerabilities-found.png" alt-text="Recomendação para remediar vulnerabilidades em configurações de segurança de contentores ":::

1. Para visualizar e remediar os controlos do CIS que um hospedeiro específico falhou, selecione o anfitrião que pretende investigar. 

    > [!TIP]
    > Se começou na página de inventário de ativos e chegou a esta recomendação a partir daí, seleci o botão **de ação Take** na página de recomendação.
    >
    > :::image type="content" source="./media/monitor-container-security/host-security-take-action-button.png" alt-text="Tome o botão de ação para lançar Log Analytics":::

    O Log Analytics abre com uma operação personalizada pronta a ser executada. A consulta personalizada padrão inclui uma lista de todas as regras falhadas que foram avaliadas, juntamente com diretrizes para ajudá-lo a resolver os problemas.

    :::image type="content" source="./media/monitor-container-security/docker-host-vulnerabilities-in-query.png" alt-text="Registar página de Analytics com a consulta mostrando todos os controlos de CIS falhados":::

1. Ajuste os parâmetros de consulta, se necessário.

1. Quando tiver a certeza de que o comando é adequado e pronto para o seu anfitrião, selecione **Run**.


## <a name="next-steps"></a>Próximos passos

O endurecimento do Docker é apenas um aspeto das funcionalidades de segurança do Centro de Segurança. 

Saiba mais [segurança no Centro de Segurança.](container-security.md)