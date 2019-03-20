---
title: Proteção de rede adaptativos no Centro de segurança do Azure | Documentos da Microsoft
description: " Aprenda a ativar a proteção de rede adaptativos no Centro de segurança do Azure. "
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: monhaber
ms.assetid: 09d62d23-ab32-41f0-a5cf-8d80578181dd
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/13/2019
ms.author: monhaber
ms.openlocfilehash: cede54f69fbeec5e01c17d84436bdc4c9ee91002
ms.sourcegitcommit: f596d88d776a3699f8c8cf98415eb874187e2a48
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2019
ms.locfileid: "58117066"
---
# <a name="adaptive-network-hardening-in-azure-security-center"></a>Proteção de rede adaptativos no Centro de segurança do Azure
Saiba como configurar o sistema de proteção de rede adaptativos no Centro de segurança do Azure.

## <a name="what-is-adaptive-network-hardening"></a>O que é o sistema de proteção de rede adaptável?
Aplicando [grupos de segurança (NSG) de rede](https://docs.microsoft.com/azure/virtual-network/security-overview) filtrar o tráfego de e para recursos, melhora a sua postura de segurança de rede. No entanto, ainda pode haver alguns casos em que o tráfego real que flui através do NSG é um subconjunto das regras de NSG definido. Nestes casos, melhorar ainda mais a postura de segurança pode ser conseguido através das regras NSG, com base nos padrões de tráfego real do sistema de proteção.

Sistema de proteção de rede adaptável fornece recomendações para proteger ainda mais as regras do NSG. Ele usa um algoritmo de machine learning que tenha em consideração no tráfego real, conhecido configuração confiável, informações sobre ameaças e outros indicadores de comprometimento, e, em seguida, fornece recomendações para permitir tráfego apenas das tuplas de porta/IP específicos.

Por exemplo, digamos que a regra NSG existente é permitir o tráfego de 140.20.30.10/24 na porta 22. Recomendação do o estímulo rede sistema de proteção, com base na análise, seria restringir o intervalo e permitir o tráfego 140.23.30.10/29 – que é um intervalo IP mais estreito, e negar todos os outros tráfegos para essa porta.

![Vista de sistema de proteção de rede](./media/security-center-adaptive-network-hardening/traffic-hardening.png)


> [!NOTE]
> Recomendações de sistema de proteção de rede adaptáveis são suportadas nas seguintes portas: 22, 3389, 21, 23, 445, 4333, 3306, 1433, 1434, 53, 20, 5985, 5986, 5432, 139, 66, 1128

## <a name="view-adaptive-network-hardening-alerts-and-rules"></a>Ver alertas de sistema de proteção de rede adaptável e regras

1. No Centro de segurança, selecione **Networking** -> **sistema de proteção de rede adaptável**. As VMs de rede estão listadas na três separadores separadas:
   * **Recursos de mau estado de funcionamento**: VMs que têm atualmente recomendações e alertas que foram acionados executando o algoritmo de sistema de proteção de rede adaptável. 
   * **Recursos de bom estado de funcionamento**: VMs sem alertas e recomendações.
   * **Não verificados recursos**: VMs que o algoritmo de sistema de proteção de rede adaptável não pode ser executado devido a uma das seguintes razões:
      * **As VMs são VMs clássicas**:-apenas VMs do Azure Resource Manager são suportadas.
      * **Não existem dados suficientes estão disponíveis**: Para gerar tráfego preciso recomendações de proteção, o Centro de segurança requer, pelo menos, 30 dias de dados de tráfego.
      * **A VM não está protegida pelo padrão ASC**: Apenas as VMs que estão definidas para o escalão de preço Standard do Centro de segurança são elegíveis para esta funcionalidade.

     ![recursos de mau estado de funcionamento](./media/security-center-adaptive-network-hardening/unhealthy-resources.png)

2. Partir do **recursos mau estado de funcionamento** separador, selecione uma VM para ver de alertas e as regras de proteção recomendada para aplicar.

    ![alertas de sistema de proteção](./media/security-center-adaptive-network-hardening/hardening-alerts.png)


## <a name="review-and-apply-adaptive-network-hardening-recommended-rules"></a>Rever e aplicar a proteção de rede adaptável recomendado regras

1. Partir do **recursos mau estado de funcionamento** separador, selecione uma VM. Os alertas e as regras de proteção recomendada são listadas.
   ![alertas de sistema de proteção](./media/security-center-adaptive-network-hardening/hardening-alerts.png)

   > [!NOTE]
   > O **regras** separador lista as regras que o sistema de proteção de rede adaptável recomenda a adicionar. O **alertas** guia lista os alertas que foram gerados em virtude de tráfego, que flui para o recurso, que não está dentro do intervalo IP permitido nas regras de recomendada.

   ![regras de proteção](./media/security-center-adaptive-network-hardening/hardening-rules.png)

2. Se quiser alterar alguns dos parâmetros de uma regra, pode modificá-la, conforme explicado [modificar uma regra](#modify-rule).
   > [!NOTE]
   > Também pode [elimine](#delete-rule) ou [adicionar](#add-rule) uma regra.

3. Selecione as regras que pretende aplicar o NSG e clique em **impor**. 

### Modificar uma regra  <a name ="modify-rule"> </a>

Pode querer modificar os parâmetros de uma regra que foi recomendado. Por exemplo, pode querer alterar os intervalos IP recomendados.

Algumas diretrizes importantes para modificar uma regra de sistema de proteção de rede adaptável:

* Pode modificar os parâmetros de regras "Permitir". 
* Não é possível alterar as regras para se tornar "Negar" regras "Permitir". 

  > [!NOTE]
  > Criar e modificar "Negar" regras é feito diretamente no NSG para obter mais detalhes, consulte [criação, alteração ou eliminar um grupo de segurança de rede](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group).

* R **negar todo o tráfego** regra é o único tipo de regra "Negar" que seriam listada aqui, e não pode ser modificado. Pode, no entanto, pode eliminá-la (veja [eliminar uma regra](#delete-rule)).
  > [!NOTE]
  > R **negar todo o tráfego** regra é recomendada quando, como resultado de executar o algoritmo, o Centro de segurança não identificar o tráfego que deve ser permitido, com base na configuração de NSG existente. Portanto, a regra recomendada é negar todo o tráfego para a porta especificada. O nome deste tipo de regra é apresentado como "gerada pelo sistema". Depois de impor esta regra, o seu nome real no NSG será uma cadeia de caracteres composta o protocolo, a direção do tráfego, "NEGAR" e um número aleatório.

*Para modificar uma regra de sistema de proteção de rede adaptável:*

1. Para modificar alguns dos parâmetros de uma regra no **regras** separador, clique nas reticências (...) no final da linha da regra e clique em **Editar regra**.

   ![Editar regra](./media/security-center-adaptive-network-hardening/edit-hard-rule.png)

1. Na **Editar regra** janela, atualizar os detalhes que pretende alterar e clique em **guardar**.

   > [!NOTE]
   > Depois de clicar em **guardar**, alterou com êxito a regra. *No entanto, não aplicou-lo para o NSG.* Para aplicá-la, tem de selecionar a regra na lista e clique em **impor** (conforme explicado na próxima etapa).

3. Para aplicar a regra atualizada, na lista, selecione a regra atualizada e clique em **impor**.

### Adicionar uma nova regra <a name ="add-rule"> </a>


Pode adicionar uma regra "Permitir" que não foi recomendada pelo centro de segurança.

> [!NOTE]
> Regras de apenas "Permitir" podem ser adicionadas aqui. Se pretender adicionar "Negar" regras, pode fazer diretamente no NSG. Para obter mais detalhes, consulte [criação, alteração ou eliminar um grupo de segurança de rede](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group).

*Para adicionar uma regra de sistema de proteção de rede adaptável:*

1. Clique em **Adicionar regra** (localizado no canto superior esquerdo).

   ![Adicionar regra](./media/security-center-adaptive-network-hardening/add-hard-rule.png)

1. Na **Editar regra** janela, introduza os detalhes e clique em **guardar**.

   > [!NOTE]
   > Depois de clicar em **guardar**, adicionou com êxito a regra e, é apresentada com outras regras recomendadas. No entanto, não aplicou-lo no NSG. Para ativá-lo, tem de selecionar a regra na lista e clique em **impor** (conforme explicado na próxima etapa).

3. Para aplicar a nova regra, na lista, selecione a nova regra e clique em **impor**.



### Eliminar uma regra <a name ="delete-rule"> </a>

Quando for necessário, pode eliminar uma regra recomendada. Por exemplo, pode determinar que aplicar uma regra sugerida pode bloquear o tráfego legítimo.

*Para eliminar uma regra de sistema de proteção de rede adaptável:*

1. Na **regras** separador, clique nas reticências (...) no final da linha da regra e clique em **Eliminar regra**.

   ![Eliminar regra](./media/security-center-adaptive-network-hardening/delete-hard-rule.png)







 

