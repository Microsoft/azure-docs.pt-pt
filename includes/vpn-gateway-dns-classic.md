---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/08/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0c5e3a095b312d36cb05a78e84a13bc2a73d95d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91875595"
---
As definições de DNS não são uma parte necessária desta configuração, mas o DNS é necessário se quiser uma resolução de nome entre os seus VMs. A especificação de um valor não cria um novo servidor DNS. O endereço IP do servidor DNS que especificar deve ser um servidor DNS que possa resolver os nomes dos recursos a que se está a ligar.

Depois de criar a rede virtual, pode adicionar o endereço IP de um servidor DNS, para lidar com a resolução de nomes. Abra as definições para a sua rede virtual, selecione servidores DNS e adicione o endereço IP do servidor DNS que pretende utilizar para a resolução de nomes.

1. Localize a rede virtual no portal.
2. Na página da sua rede virtual, na secção **Definições,** selecione **servidores DNS**.
3. Adicione um servidor DNS.
4. Para guardar as suas definições, **selecione Guardar** no topo da página.