---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/27/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e96d205ef1a8f94baa3a0cfe6c5127b6cf570e5a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80420964"
---
Este artigo explica como enviar um VHD da sua máquina local para um disco gerido azure ou copiar um disco gerido para outra região, utilizando o AzCopy. Este processo, upload direto, também permite carregar um VHD até 32 TiB em tamanho diretamente em um disco gerido. Atualmente, o upload direto é suportado para hdd padrão, SSD padrão e discos geridos premium SSD. Ainda não é suportado por discos ultra.

Se estiver a fornecer uma solução de backup para IaaS VMs em Azure, recomendamos a utilização de upload direto para restaurar as cópias de segurança do cliente para discos geridos. Ao carregar um VHD de uma fonte externa a Azure, as velocidades dependeriam da largura de banda local. Ao carregar ou copiar a partir de um VM Azure, então a sua largura de banda seria a mesma que os HDDs padrão.