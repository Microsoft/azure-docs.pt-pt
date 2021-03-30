---
title: Ações proibidas durante o acesso elevado
description: O VMware Engine reverte as alterações para garantir que o serviço permanece ininterrupto quando o VMware Engine deteta qualquer uma das seguintes ações proibidas.
titleSuffix: Azure VMware Solution by CloudSimple
ms.date: 10/28/2020
ms.topic: article
ms.service: azure-vmware-cloudsimple
author: divka78
ms.author: dikamath
ms.openlocfilehash: 81a09a9cfe749de56f7306a8d5e1ae5db01295fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92915437"
---
# <a name="forbidden-actions-during-elevated-access"></a>Ações proibidas durante o acesso elevado

Durante o intervalo de tempo de elevação, algumas ações são proibidas. Quando o VMware Engine deteta qualquer uma das seguintes ações proibidas, o VMware Engine reverte as alterações para garantir que o serviço permanece ininterrupto.

## <a name="cluster-actions"></a>Ações de cluster

- Remover um cluster do vCenter.
- Alterando a vSphere Alta Disponibilidade (HA) em um cluster.
- Adicionar um hospedeiro ao cluster do vCenter.
- Remover um hospedeiro do cluster do vCenter.

## <a name="host-actions"></a>Ações de anfitrião

- Remover datastores num hospedeiro ESXi.
- Desinstalar o agente vCenter do anfitrião.
- Modificação da configuração do hospedeiro.
- Fazendo quaisquer alterações nos perfis do anfitrião.
- Colocação de um hospedeiro em modo de manutenção.

## <a name="network-actions"></a>Ações de rede

- Eliminação do interruptor virtual distribuído predefinido (DVS) numa nuvem privada.
- Remover um hospedeiro do DVS predefinido.
- Importando qualquer definição de DVS.
- Reconfigurar qualquer definição de DVS.
- A atualizar os DVS.
- Apagar o grupo de gestão.
- Edição do grupo portuário de gestão.

## <a name="roles-and-permissions-actions"></a>Funções e permissões ações

- Criar um papel global.
- Modificar ou eliminar a permissão para quaisquer objetos de gestão.
- Modificar ou remover quaisquer funções predefinidoras.
- Aumente os privilégios de um papel para mais alto do que o Cloud-Owner-Role.

## <a name="other-actions"></a>Outras ações

- Remoção de quaisquer licenças predefinidos:
  - vCenter Server
  - Nódoas ESXi
  - NSX-T
  - HCX
- Modificar ou eliminar o conjunto de recursos de gestão.
- VMs de gestão de clonagem.


## <a name="next-steps"></a>Passos seguintes
[CloudSimple manutenção e atualizações](cloudsimple-maintenance-updates.md) 
