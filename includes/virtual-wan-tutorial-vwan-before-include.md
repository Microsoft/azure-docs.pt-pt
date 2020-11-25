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
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023533"
---
Antes de iniciar a sua configuração, verifique se cumpre os seguintes critérios:

* Se já tem rede virtual a que pretende ligar, verifique se nenhuma das sub-redes da sua rede no local se sobrepõe à rede. A sua rede virtual não necessita de uma sub-rede de gateway e não pode ter quaisquer gateways de rede virtuais. Se não tiver uma rede virtual, pode criar uma utilizando os passos deste artigo.
* Obtenha um intervalo de endereços IP para a região do seu hub. O hub é uma rede virtual, e a gama de endereços que especifica para a região do hub não pode sobrepor-se a uma rede virtual existente a que se conecta. Também não pode sobrepor-se aos intervalos de endereços que se conectam ao local. Se não estiver familiarizado com os intervalos de endereços IP localizados na configuração da rede no local, coordene com alguém que possa fornecer esses detalhes para si.
* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.