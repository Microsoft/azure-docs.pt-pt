---
title: incluir ficheiro
description: incluir ficheiro
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 08/10/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 2b61cc8c5c448c28e96b06afa3556688a82567ed
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2019
ms.locfileid: "64866815"
---
### <a name="delete-local-resources"></a>Eliminar recursos locais

Se quiser remover o runtime do IoT Edge e os recursos relacionados do seu dispositivo, utilize os comandos adequados para o sistema operativo do mesmo. 

#### <a name="windows"></a>Windows

Desinstale o runtime do IoT Edge.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Uninstall-SecurityDaemon
   ```

Quando o runtime do IoT Edge é removido, os contentores que ele criou são parados, mas continuam no seu dispositivo. Veja todos os contentores.

   ```powershell
   docker ps -a
   ```

Elimine os contentores de runtime criados no seu dispositivo.

   ```powershell
   docker rm -f edgeHub
   docker rm -f edgeAgent
   ```

Veja os nomes dos contentores para eliminar os contentores adicionais que foram apresentados na saída `docker ps`. 

#### <a name="linux"></a>Linux

Remova o runtime do IoT Edge.

   ```bash
   sudo apt-get remove --purge iotedge
   ```

Quando o runtime do IoT Edge é removido, os contentores que ele criou são parados, mas continuam no seu dispositivo. Veja todos os contentores.

   ```bash
   sudo docker ps -a
   ```

Elimine os contentores de runtime criados no seu dispositivo.

   ```bash
   docker rm -f edgeHub
   docker rm -f edgeAgent
   ```

Veja os nomes dos contentores para eliminar os contentores adicionais que foram apresentados na saída `docker ps`. 

Remova o runtime do contentor.

   ```bash
   sudo apt-get remove --purge moby
   ```