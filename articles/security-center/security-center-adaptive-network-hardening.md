---
title: Endurecimento de rede adaptativa no Centro de Segurança Azure Microsoft Docs
description: Aprenda a usar padrões de tráfego reais para endurecer as regras dos seus grupos de segurança de rede (NSG) e melhorar ainda mais a sua postura de segurança.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 09d62d23-ab32-41f0-a5cf-8d80578181dd
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/11/2020
ms.author: memildin
ms.openlocfilehash: e2b17e15c5548b4c9b93a62a7d4dfe62ff44404c
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92341758"
---
# <a name="adaptive-network-hardening-in-azure-security-center"></a>Endurecimento de rede adaptativa no Centro de Segurança Azure
Aprenda a configurar o endurecimento adaptativo da rede no Centro de Segurança.

## <a name="availability"></a>Disponibilidade
|Aspeto|Detalhes|
|----|:----|
|Estado de libertação:|Geralmente disponível (GA)|
|Preços:|Requer [Azure Defender para servidores](defender-for-servers-introduction.md)|
|Funções e permissões necessárias:|Escreva permissões nos NSGs da máquina|
|Nuvens:|![Yes](./media/icons/yes-icon.png) Nuvens comerciais<br>![No](./media/icons/no-icon.png) Nacional/Soberano (Gov dos EUA, China Gov, Outro Gov)|
|||

## <a name="what-is-adaptive-network-hardening"></a>O que é o endurecimento adaptativo da rede?
A aplicação de grupos de segurança de [rede (NSG)](../virtual-network/network-security-groups-overview.md) para filtrar o tráfego de e para os recursos, melhora a sua postura de segurança de rede. No entanto, ainda pode haver alguns casos em que o tráfego real que flui através do NSG é um subconjunto das regras do NSG definidos. Nestes casos, melhorar ainda mais a postura de segurança pode ser alcançado endurecendo as regras do NSG, com base nos padrões reais de tráfego.

O endurecimento da rede adaptativa fornece recomendações para endurecer ainda mais as regras do NSG. Usa um algoritmo de aprendizagem automática que fatores no tráfego real, configuração confiável conhecida, inteligência de ameaça e outros indicadores de compromisso, e depois fornece recomendações para permitir o tráfego apenas a partir de tuples IP/port específico.

Por exemplo, digamos que a regra nSG existente é permitir o tráfego a partir de 140.20.30.10/24 na porta 22. Com base na análise de tráfego, o endurecimento adaptativo da rede pode recomendar o estreitamento da gama para permitir o tráfego a partir de 140.23.30.10/29, e negar todo o tráfego para aquele porto.

>[!Note]
> As recomendações de endurecimento da rede adaptativa só são suportadas nas seguintes portas específicas (tanto para a UDP como para a TCP): 13, 17, 19, 22, 23, 53, 69, 81, 111, 119, 123, 135, 137, 138, 139, 161, 162, 389, 445, 512, 514, 593, 636, 873, 1433, 1434, 1900, 2049, 2301, 2323, 2381, 3268, 3306, 2306 3389, 4333, 5353, 5432, 5555, 5800, 5900, 5900, 5985, 5986, 6379 , 6379, 7000, 7001, 7199, 8081, 8089, 8545, 9042, 9160, 9300, 11211, 16379, 26379, 27017, 37215


## <a name="view-and-manage-hardening-alerts-and-rules"></a>Ver e gerir alertas e regras de endurecimento

1. A partir do menu do Security Center, abra o painel **Azure Defender** e selecione o azulejo de endurecimento de rede adaptativo (1), ou o item do painel de insights relacionado com o endurecimento adaptativo da rede (2). 

    :::image type="content" source="./media/security-center-adaptive-network-hardening/traffic-hardening.png" alt-text="Aceder às ferramentas de endurecimento de rede adaptativas" lightbox="./media/security-center-adaptive-network-hardening/traffic-hardening.png":::

    > [!TIP]
    > O painel de insights mostra a percentagem dos seus VMs atualmente defendidos com o endurecimento adaptativo da rede. 

1. A página de detalhes para as **recomendações de endurecimento da rede adaptativa deve ser aplicada na** internet face à recomendação de máquinas virtuais aberta com os VMs da sua rede agrupados em três separadores:
   * **Recursos insalubres**: VMs que atualmente têm recomendações e alertas que foram desencadeados pela execução do algoritmo de endurecimento da rede adaptativa. 
   * **Recursos saudáveis**: VMs sem alertas e recomendações.
   * **Recursos não identificados**: VMs que o algoritmo de endurecimento da rede adaptativa não pode ser executado devido a uma das seguintes razões:
      * **Os VMs são VMs clássicos**: Apenas VMs Azure Resource Manager são suportados.
      * **Não existem dados suficientes**: Para gerar recomendações precisas de endurecimento do tráfego, o Centro de Segurança requer pelo menos 30 dias de dados de tráfego.
      * **O VM não está protegido pelo Azure Defender**: Apenas VMs protegidos com [Azure Defender para servidores](defender-for-servers-introduction.md) são elegíveis para esta funcionalidade.

    :::image type="content" source="./media/security-center-adaptive-network-hardening/recommendation-details-page.png" alt-text="Aceder às ferramentas de endurecimento de rede adaptativas":::

1. A partir do separador **recursos insalubres,** selecione um VM para visualizar os seus alertas e as regras de endurecimento recomendadas para aplicar.

    - O separador **Regras** lista as regras que o endurecimento adaptativo da rede recomenda que adicione
    - O separador **Alertas** enumera os alertas gerados devido ao tráfego, fluindo para o recurso, que não está dentro do intervalo de IP permitido nas regras recomendadas.

1. Opcionalmente, edite as regras:

    - [Modificar uma regra](#modify-rule)
    - [Eliminar uma regra](#delete-rule) 
    - [Adicionar uma regra](#add-rule)

3. Selecione as regras que pretende aplicar no NSG e clique em **'Impor'.**

    > [!TIP]
    > Se os intervalos de IP de origem permitidas tão bem como 'Nenhum', significa que a regra recomendada é uma regra *de negação,* caso contrário, é uma regra *de permitir.*

    :::image type="content" source="./media/security-center-adaptive-network-hardening/hardening-alerts.png" alt-text="Aceder às ferramentas de endurecimento de rede adaptativas":::

      > [!NOTE]
      > As regras aplicadas são adicionadas ao NSG(s) que protege o VM. (Um VM pode ser protegido por um NSG associado ao seu NIC, ou à sub-rede em que o VM reside, ou ambos)

### <a name="modify-a-rule"></a>Modificar <a name ="modify-rule"> </a> uma regra

Pode querer modificar os parâmetros de uma regra que foi recomendada. Por exemplo, pode querer alterar os intervalos de IP recomendados.

Algumas orientações importantes para modificar uma regra de endurecimento adaptativo da rede:

* Só pode modificar os parâmetros das regras de "permitir". 
* Não é possível alterar regras de "permitir" que se tornem regras de "negar". 

  > [!NOTE]
  > A criação e modificação das regras de "negação" é feita diretamente no NSG. Para obter mais informações, consulte [Criar, alterar ou eliminar um grupo de segurança de rede](../virtual-network/manage-network-security-group.md).

* A **Negar toda** a regra de tráfego é o único tipo de regra de "negar" que seria listada aqui, e não pode ser modificada. No entanto, pode eliminá-lo (ver [Eliminar uma regra).](#delete-rule)
  > [!NOTE]
  > **Recomenda-se toda a** regra de tráfego quando, como resultado da execução do algoritmo, o Centro de Segurança não identifica o tráfego que deve ser permitido, com base na configuração NSG existente. Portanto, a regra recomendada é negar todo o tráfego para o porto especificado. O nome deste tipo de regra é apresentado como "*System Generated*". Após a aplicação desta regra, o seu nome real no NSG será uma cadeia composta pelo protocolo, direção de tráfego, "DENY", e um número aleatório.

*Para modificar uma regra de endurecimento de rede adaptativa:*

1. Para modificar alguns dos parâmetros de uma regra, no separador **Regras,** clique nos três pontos (...) no final da linha da regra e clique em **Editar**.

   ![Regra de edição](./media/security-center-adaptive-network-hardening/edit-hard-rule.png)

1. Na janela **de regras Editar,** atualize os detalhes que pretende alterar e clique em **Guardar**.

   > [!NOTE]
   > Depois de clicar em **Guardar,** alterou com sucesso a regra. *No entanto, não o aplicou ao NSG.* Para aplicá-la, tem de selecionar a regra da lista e selecionar **Enforce** (conforme explicado no passo seguinte).

   ![Selecionando Guardar](./media/security-center-adaptive-network-hardening/edit-hard-rule3.png)

3. Para aplicar a regra atualizada, a partir da lista, selecione a regra atualizada e clique em **Executar**.

    ![impor regra](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)

### <a name="add-a-new-rule"></a>Adicione uma <a name ="add-rule"> </a> nova regra

Pode adicionar uma regra de "permitir" que não foi recomendada pelo Centro de Segurança.

> [!NOTE]
> Só as regras de "permitir" podem ser adicionadas aqui. Se quiser adicionar regras de "negar", pode fazê-lo diretamente no NSG. Para obter mais informações, consulte [Criar, alterar ou eliminar um grupo de segurança de rede](../virtual-network/manage-network-security-group.md).

*Para adicionar uma regra de endurecimento de rede adaptativa:*

1. Clique **na regra 'Adicionar'** (localizada no canto superior esquerdo).

   ![adicionar regra](./media/security-center-adaptive-network-hardening/add-hard-rule.png)

1. Na janela **nova regra,** insira os detalhes e clique em **Adicionar**.

   > [!NOTE]
   > Depois de clicar em **Add,** adicionou com sucesso a regra e está listado com as outras regras recomendadas. No entanto, não o aplicou no NSG. Para ativá-la, tem de selecionar a regra da lista e clicar em **"Executar"** (conforme explicado no passo seguinte).

3. Para aplicar a nova regra, da lista, selecione a nova regra e clique em **Impor**.

    ![impor regra](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)


### <a name="delete-a-rule"></a>Eliminar uma <a name ="delete-rule"> </a> regra

Quando necessário, pode eliminar uma regra recomendada para a sessão atual. Por exemplo, pode determinar que a aplicação de uma regra sugerida pode bloquear o tráfego legítimo.

*Para eliminar uma regra de endurecimento de rede adaptativa para a sua sessão atual:*

1. No separador **Regras,** clique nos três pontos (...) no final da linha da regra e clique em **Apagar**.  

    ![Apagar uma regra](./media/security-center-adaptive-network-hardening/delete-hard-rule.png)