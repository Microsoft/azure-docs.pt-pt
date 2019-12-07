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
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896626"
---
Antes de iniciar sua configuração, verifique se você atende aos seguintes critérios:

* Se você já tiver uma rede virtual à qual deseja se conectar, verifique se nenhuma das sub-redes da sua rede local se sobrepõem. Sua rede virtual não requer uma sub-rede de gateway e não pode ter nenhum gateway de rede virtual. Se você não tiver uma rede virtual, poderá criar uma usando as etapas neste artigo.
* Obtenha um intervalo de endereços IP para a região do seu hub. O Hub é uma rede virtual e o intervalo de endereços especificado para a região do Hub não pode se sobrepor a uma rede virtual existente à qual você se conecta. Ele também não pode se sobrepor aos intervalos de endereços que você conecta ao local. Se você não estiver familiarizado com os intervalos de endereços IP localizados em sua configuração de rede local, coordene com alguém que possa fornecer esses detalhes para você.
* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.