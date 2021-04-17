---
author: v-dalc
ms.service: databox
ms.author: alkohli
ms.topic: include
ms.date: 04/14/2021
ms.openlocfilehash: a33a702128be0d8e90b3db635001dbfbdb279f2a
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575953"
---
|Tipo de imagem  |Generalizada  |Especializada  |
|---------|---------|---------|
|Destino     |Implantado em qualquer sistema.         | Direcionado para um sistema específico.        |
|Configuração após arranque     | Configuração necessária no primeiro arranque do VM.          | Não é preciso armação. <br> A plataforma liga o VM.        |
|Configuração     |Nome de anfitrião, utilizador de administração e outras definições específicas de VM necessárias.         |Pré-configurado.         |
|Usado quando     |Criar vários novos VMs a partir da mesma imagem.         |Migrar uma máquina específica ou restaurar um VM de cópias de segurança anteriores.         |
