---
title: Rede adaptável endurecendo no Centro de Segurança Azure [ Microsoft Docs
description: Aprenda a usar padrões de tráfego reais para endurecer as regras dos seus grupos de segurança de rede (NSG) e melhorar ainda mais a sua postura de segurança.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 09d62d23-ab32-41f0-a5cf-8d80578181dd
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/11/2020
ms.author: memildin
ms.openlocfilehash: a75be23e2e8215d86aebcfd7f4317f2f597d3c5b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80385083"
---
# <a name="adaptive-network-hardening-in-azure-security-center"></a>Rede adaptável endurecendo no Centro de Segurança Azure
Saiba como configurar o endurecimento da rede adaptável no Centro de Segurança Azure.

## <a name="what-is-adaptive-network-hardening"></a>O que é o Endurecimento da Rede Adaptável?
Aplicação de grupos de segurança de [rede (NSG)](https://docs.microsoft.com/azure/virtual-network/security-overview) para filtrar o tráfego de e para recursos, melhora a sua postura de segurança da rede. No entanto, ainda pode haver alguns casos em que o tráfego real que flui através do NSG é um subconjunto das regras nsg definidas. Nestes casos, uma melhoria adicional da postura de segurança pode ser alcançada através do endurecimento das regras do NSG, com base nos padrões reais de tráfego.

O Endurecimento da Rede Adaptável fornece recomendações para endurecer ainda mais as regras do NSG. Usa um algoritmo de aprendizagem automática que fatores no tráfego real, configuração fidedigna conhecida, inteligência de ameaça, e outros indicadores de compromisso, e, em seguida, fornece recomendações para permitir o tráfego apenas a partir de tuples IP/porta específicos.

Por exemplo, digamos que a regra nsg existente é permitir o tráfego a partir de 140.20.30.10/24 na porta 22. A recomendação da Rede Adaptativa de Endurecimento, com base na análise, seria reduzir o intervalo e permitir o tráfego a partir de 140.23.30.10/29 – que é uma gama IP mais estreita, e negar todo o tráfego para aquele porto.

>[!TIP]
> Recomendações de endurecimento da rede adapttiva só são suportadas nas seguintes portas específicas (tanto para a UDP como para a TCP): 13, 17, 19, 22, 23, 53, 69, 81, 111, 119, 123, 135, 137, 138, 139, 161, 162, 389, 445, 512, 514, 514, 514, 514, 514, 514, 514, 514, 514, 514, 514, 514, 514, 514, 514, 514, 514, 514, 514, 514, 514, 514, 514, 514, 514, 514, 514, 514, 514, 514, 514, 514, 514, 514, 514, 514, 514, 514, 514, 514, 514, 514, 514, 514, 514, 514, 514, 514, 514, 514, 514, 514, 514, 514, 514, 51 636, 873, 1433, 1434, 1900, 2049, 2301, 2323, 2381, 3268, 3306, 3389, 4333, 5353, 5432, 5555, 5800, 5900, 5900, 5985, 5986, 698, 6989, 6989, 6989, 6989, 6989, 6986, 6900, 5900 , 6379, 7000, 7001, 7199, 8081, 8089, 8545, 9042, 9160, 9300, 11211, 16379, 26379, 27017, 37215


![Visão de endurecimento da rede](./media/security-center-adaptive-network-hardening/traffic-hardening.png)


## <a name="view-adaptive-network-hardening-alerts-and-rules"></a>Ver alertas e regras de endurecimento da rede adapttiva

1. No Centro de Segurança, selecione O**endurecimento**adaptativo da rede de rede em **rede** -> . Os VMs da rede estão listados em três separadores separados:
   * **Recursos pouco saudáveis**: VMs que atualmente têm recomendações e alertas que foram desencadeados pela execução do algoritmo adaptativo de endurecimento da rede. 
   * **Recursos saudáveis**: VMs sem alertas e recomendações.
   * **Recursos não digitalizados**: VMs que o algoritmo adaptativo de endurecimento da rede não pode ser executado por uma das seguintes razões:
      * **VMs são VMs clássicos**: Apenas os VMs do Gestor de Recursos Azure são suportados.
      * **Não existem dados suficientes**: Para gerar recomendações precisas de endurecimento do tráfego, o Centro de Segurança necessita de pelo menos 30 dias de dados de tráfego.
      * **VM não está protegido pela norma ASC**: Apenas os VMs que são definidos para o nível de preços Standard do Security Center são elegíveis para esta funcionalidade.

     ![recursos insalubres](./media/security-center-adaptive-network-hardening/unhealthy-resources.png)

2. A partir do separador **de recursos não saudáveis,** selecione um VM para ver os seus alertas e as regras de endurecimento recomendadas para aplicar.

    ![alertas de endurecimento](./media/security-center-adaptive-network-hardening/anh-recommendation-rules.png)


## <a name="review-and-apply-adaptive-network-hardening-recommended-rules"></a>Rever e aplicar regras recomendadas para endurecer a rede adaptável

1. A partir do separador **de recursos não saudáveis,** selecione um VM. Os alertas e as regras de endurecimento recomendadas estão listados.

     ![endurecimento das regras](./media/security-center-adaptive-network-hardening/hardening-alerts.png)

   > [!NOTE]
   > O separador **Regras** lista as regras que o Endurecimento da Rede Adaptável recomenda adicionar. O separador **Alertas** lista os alertas que foram gerados devido ao tráfego, fluindo para o recurso, que não está dentro da gama IP permitida nas regras recomendadas.

2. Se quiser alterar alguns dos parâmetros de uma regra, pode modificá-la, como explicado em [Modificar uma regra](#modify-rule).
   > [!NOTE]
   > Também pode [excluir](#delete-rule) ou [adicionar](#add-rule) uma regra.

3. Selecione as regras que pretende aplicar no NSG e clique em **'Aplicar 'Aplicar'.**

      > [!NOTE]
      > As regras aplicadas são adicionadas ao NSG(s) que protege o VM. (Um VM pode ser protegido por um NSG associado ao seu NIC, ou a sub-rede em que o VM reside, ou ambos)

    ![impor regras](./media/security-center-adaptive-network-hardening/enforce-hard-rule2.png)


### <a name="modify-a-rule"></a>Modificar uma <a name ="modify-rule"> </a> regra

Pode querer modificar os parâmetros de uma regra recomendada. Por exemplo, pode querer alterar as gamas ip recomendadas.

Algumas orientações importantes para modificar uma regra de endurecimento da rede adaptativa:

* Só pode modificar os parâmetros das regras de "permitir". 
* Não se pode alterar as regras de "permitir" tornar-se regras de "negar". 

  > [!NOTE]
  > A criação e modificação das regras de "negar" é feita diretamente no NSG. Para mais informações, consulte [Criar, alterar ou eliminar um grupo](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)de segurança de rede .

* A **Deny all traffic** rule é o único tipo de regra de "negar" que seria listada aqui, e não pode ser modificada. Pode, no entanto, eliminá-la (ver [Eliminar uma regra](#delete-rule)).
  > [!NOTE]
  > A **Deny toda a** regra de tráfego é recomendada quando, como resultado da execução do algoritmo, o Security Center não identifica o tráfego que deve ser permitido, com base na configuração nsg existente. Por conseguinte, a regra recomendada é negar todo o tráfego à porta especificada. O nome deste tipo de regra é apresentado como "*Sistema Gerado*". Após a aplicação desta regra, o seu nome real no NSG será uma sequência composta pelo protocolo, direção de trânsito, "DENY", e um número aleatório.

*Para modificar uma regra de endurecimento da rede adaptativa:*

1. Para modificar alguns dos parâmetros de uma regra, no separador **Regras,** clique nos três pontos (...) no final da linha da regra, e clique em **Editar**.

   ![editar regra](./media/security-center-adaptive-network-hardening/edit-hard-rule.png)

1. Na janela de **regras Editar,** atualize os detalhes que pretende alterar e clique em **Guardar**.

   > [!NOTE]
   > Depois de clicar em **Save,** mudou com sucesso a regra. *No entanto, não o aplicou ao NSG.* Para aplicá-lo, deve selecionar a regra na lista e clicar em **'Fazer'** (conforme explicado no próximo passo).

   ![editar regra](./media/security-center-adaptive-network-hardening/edit-hard-rule3.png)

3. Para aplicar a regra atualizada, a partir da lista, selecione a regra atualizada e clique em **'Aplicar**' .

    ![impor regra](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)

### <a name="add-a-new-rule"></a>Adicione uma <a name ="add-rule"> </a> nova regra

Pode adicionar uma regra de "permitir" que não foi recomendada pelo Centro de Segurança.

> [!NOTE]
> Só as regras de "permitir" podem ser adicionadas aqui. Se quiser adicionar regras de "negar", pode fazê-lo diretamente no NSG. Para mais informações, consulte [Criar, alterar ou eliminar um grupo](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)de segurança de rede .

*Para adicionar uma regra de endurecimento da rede adaptável:*

1. Clique em **Adicionar regra** (localizada no canto superior esquerdo).

   ![adicionar regra](./media/security-center-adaptive-network-hardening/add-hard-rule.png)

1. Na nova janela de **regras,** introduza os detalhes e clique em **Adicionar**.

   > [!NOTE]
   > Depois de clicar em **Adicionar,** adicionou com sucesso a regra e está listada com as outras regras recomendadas. No entanto, não o aplicou no NSG. Para o ativar, deve selecionar a regra da lista e clicar em **'Fazer'(** conforme explicado no próximo passo).

3. Para aplicar a nova regra, a partir da lista, selecione a nova regra e clique em **Aplicar**.

    ![impor regra](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)


### <a name="delete-a-rule"></a>Apagar uma <a name ="delete-rule"> </a> regra

Quando necessário, pode eliminar uma regra recomendada para a sessão atual. Por exemplo, pode determinar que aplicar uma regra sugerida pode bloquear o tráfego legítimo.

*Para eliminar uma regra de endurecimento da rede adaptável para a sua sessão atual:*

1. No separador **Regras,** clique nos três pontos (...) no final da linha da regra e clique em **Eliminar**.  

    ![endurecimento das regras](./media/security-center-adaptive-network-hardening/delete-hard-rule.png)