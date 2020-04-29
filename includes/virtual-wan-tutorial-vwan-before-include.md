---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 09/12/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 945d701a2a7dffc259c601b4dab9fa1333ccc066
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74896626"
---
Antes de iniciar a sua configuração, verifique se cumpre os seguintes critérios:

* Se já tem rede virtual a que pretende ligar, verifique se nenhuma das subredes da sua rede no local se sobrepõe à rede no local. A sua rede virtual não requer uma sub-rede de gateway e não pode ter portais de rede virtuais. Se não tiver uma rede virtual, pode criar uma utilizando os passos deste artigo.
* Obtenha um intervalo de endereços IP para a região do seu hub. O hub é uma rede virtual, e o intervalo de endereços que especifica para a região do hub não pode sobrepor-se a uma rede virtual existente a que se conecta. Também não pode sobrepor-se aos intervalos de endereços que se ligam ao local. Se não estiver familiarizado com as gamas de endereços IP localizadas na configuração da sua rede no local, coordene com alguém que possa fornecer esses detalhes para si.
* Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.